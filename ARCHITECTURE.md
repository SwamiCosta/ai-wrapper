# Tax Calculator Engine — Architecture

> Central document for this project. Read before starting any task.

---

## Overview

Tax Calculator Engine is a system that lets a contributor submit their tax-relevant
attributes (filing status, gross income, deduction, withholding) and receive back a
detailed calculation of the U.S. federal income tax they owe or should be refunded,
including a full per-bracket breakdown. It targets tax year 2025. The initial
implementation is a single-calculation, stateless exercise — no persistence, no
authentication — scoped deliberately narrow; see "Business rules and domain" below
for exactly what is and isn't in scope.

---

## Subprojects

| Name | Type | Language | Status |
|------|------|----------|--------|
| tax-engine-api | Backend REST API | Java 17 / Spring Boot | MVP in progress |
| tax-engine-web | Frontend | HTML / vanilla JS (no styling, no framework) | Planned (stretch goal) |

---

## tax-engine-api

### Responsibilities

Owns the entire tax calculation: receives a contributor's attributes over a REST
endpoint, computes taxable income, applies the correct federal bracket table for the
contributor's filing status, computes the final amount owed or refunded, and returns
a detailed breakdown. Owns all validation of the incoming request. Does not own any
UI concern and does not (yet) own any persistence.

### Architecture

Simple layered MVC — `Controller → Service → Model` — deliberately **not** hexagonal
architecture / ports & adapters. See "Architectural Principles" below for why. Package
shape:

- `controller` — REST endpoint(s), request/response DTOs, input validation
- `service` — the calculation engine (taxable income, bracket walk, final amount)
- `model` — filing status enum, bracket table data, request/response records

Note for whichever agent scaffolds this project: this repo's own `.claude/skills/`
does **not** use the workspace's `setup-architecture` skill, because that skill
scaffolds hexagonal architecture by default. Build the MVC layout manually instead.

---

## tax-engine-web

### Responsibilities

A stretch goal for this exercise (see "Development Order"). When built: a single
unstyled HTML form that collects the contributor's attributes, POSTs them to
`tax-engine-api`, and renders the returned breakdown. No client-side validation
beyond what's needed for a usable form — the API is the source of truth for
validation.

### Stack

Plain HTML + vanilla JS. No build tooling, no framework — deliberately minimal since
styling and UX are explicitly out of scope for this exercise.

---

## Business rules and domain

This is the authoritative source for the calculation. Where this section and any
code comment disagree, this section wins until updated via PR (Skill 01).

### Scope for this exercise (mandatory vs. deferred)

**In scope (mandatory):**
- Single stateless calculation per request — no persistence, no "update an existing
  record" use case, no lookup-by-id
- Standard Deduction only
- Filing status, gross income, standard deduction, withholding
- Full per-bracket breakdown in the response
- Input validation at the Controller layer (see "Validation")

**Explicitly out of scope for this exercise:**
- Itemized Deduction — documented as a future TODO; no itemized rules are
  implemented, and none should be inferred
- Credits — not present in the request or response shape at all for the MVP; adding
  it later is an additive API change, not a breaking one, by design
- Persistence, database entities, "record already exists" checks, update/save
  operations — all deferred (see "Development Order")
- Authentication, authorization, and any other access control — nothing in this
  exercise's scope calls for it
- Logging/observability, dedicated exception hierarchies, concurrency control —
  explicitly skipped per the exercise's own instructions

### Domain model

**Filing status** — one of four values: `SINGLE`, `MARRIED_FILING_JOINTLY` (MFJ),
`MARRIED_FILING_SEPARATELY` (MFS), `HEAD_OF_HOUSEHOLD` (HOH).

**Request attributes** (all captured client-side, sent to the API):
- Contributor's name
- Personal ID — treated as a Social Security Number; validated at the Controller
  layer against the format `XXX-XX-XXXX`. Treat as sensitive PII in any future work
  that adds logging (Skill 07) — never log it in full even though logging itself is
  out of scope right now.
- Filing status (one of the four values above)
- Gross income
- Standard deduction is applied automatically based on filing status — the
  contributor does not supply a deduction amount; there is no itemized path yet
- Withholding

**Response** — the full calculation with a "show your work" breakdown: taxable
income, one entry per bracket touched (rate, range start/end, taxable amount in that
bracket, tax owed in that bracket), gross tax liability, effective rate, and final
amount.

**Money type:** every monetary value, in every layer, is `BigDecimal`. Never
`float`/`double`. Output amounts are rounded to 2 decimal places using
`RoundingMode.HALF_UP` at the point of building the response — intermediate
per-bracket math is not rounded until then, so rounding error doesn't compound
across brackets.

### Calculation

1. **Taxable income** = `max(0, GrossIncome − StandardDeduction)`. Taxable income can
   never go negative — if the standard deduction exceeds gross income, taxable
   income is zero and gross tax liability is zero. This floor is not stated
   explicitly in the source spec; it's a tax-law fact, not an inferred business rule.
2. **Gross tax liability** — walk the filing status's bracket table from lowest rate
   to highest. For each bracket the taxable income reaches, tax only the slice of
   income that falls inside that bracket's range (marginal, not flat, taxation). Stop
   as soon as taxable income no longer exceeds a bracket's lower bound. Sum each
   bracket's tax to get the total. Record every touched bracket in the breakdown.
3. **Final amount** = `GrossTaxLiability − Withholding`. Represented as a single
   signed value: **positive means the contributor owes that amount; negative means a
   refund of that amount is due.**
4. **Effective rate** = `GrossTaxLiability / TaxableIncome` (0 if taxable income is 0).

### Validation (Controller layer)

- Contributor's name: required, non-blank
- Personal ID: required, must match SSN format `XXX-XX-XXXX`
- Filing status: required, must be one of the four valid enum values
- Gross income: required, `>= 0`
- Withholding: required, `>= 0`

No authentication or authorization is implemented for this exercise.

### 2025 tax data

Federal income tax year 2025. Bracket thresholds are sourced from IRS Rev. Proc.
2024-40. Standard deduction figures below are the **post-OBBBA** amounts (the One
Big Beautiful Bill Act, enacted July 2025, raised the base standard deduction above
the original Rev. Proc. 2024-40 figures) — do not "correct" these back down to
$15,000 / $30,000 / $15,000 / $22,500; those are the superseded pre-OBBBA numbers.

**Standard deductions (2025)**

| Filing status | Standard deduction |
|---|---|
| Single | $15,750 |
| MFJ | $31,500 |
| MFS | $15,750 |
| HOH | $23,625 |

**Brackets — Single / MFS** (identical up through 24%, diverge at 35%/37%)

| Rate | Single range | MFS range |
|---|---|---|
| 10% | $0 – $11,925 | $0 – $11,925 |
| 12% | $11,926 – $48,475 | $11,926 – $48,475 |
| 22% | $48,476 – $103,350 | $48,476 – $103,350 |
| 24% | $103,351 – $197,300 | $103,351 – $197,300 |
| 32% | $197,301 – $250,525 | $197,301 – $250,525 |
| 35% | $250,526 – $626,350 | $250,526 – $375,800 |
| 37% | $626,351+ | $375,801+ |

**Brackets — MFJ**

| Rate | Range |
|---|---|
| 10% | $0 – $23,850 |
| 12% | $23,851 – $96,950 |
| 22% | $96,951 – $206,700 |
| 24% | $206,701 – $394,600 |
| 32% | $394,601 – $501,050 |
| 35% | $501,051 – $751,600 |
| 37% | $751,601+ |

**Brackets — HOH**

| Rate | Range |
|---|---|
| 10% | $0 – $17,000 |
| 12% | $17,001 – $64,850 |
| 22% | $64,851 – $103,350 |
| 24% | $103,351 – $197,300 |
| 32% | $197,301 – $250,525 |
| 35% | $250,526 – $626,350 |
| 37% | $626,351+ |

**Known-good worked example** (use as a unit test assertion): HOH, taxable income
$18,000 → 10% bracket taxes $17,000 = $1,700.00; 12% bracket taxes the remaining
$1,000 = $120.00; gross tax liability = $1,820.00; effective rate ≈ 10.11%.

---

## Architectural Principles

- **MVC, not hexagonal.** The MVP is one operation (calculate) with no external
  integrations (no database, no third-party calls) — the ports/adapters boundary
  hexagonal architecture provides has nothing to isolate yet, so it would be pure
  overhead. Revisit only if `tax-engine-api` grows real external integrations (e.g.
  persistence, an e-file service) where that boundary would actually earn its keep.
- **Stateless first.** Persistence, "record already exists" checks, and
  update-in-place are deliberately deferred (see "Development Order") rather than
  designed for prematurely.
- **Correctness over coverage.** Given the exercise's own time-boxing, the mandate is
  a small number of unit tests that prove every step of the calculation is correct
  (Skill 03), not exhaustive coverage.

---

## Inter-service Communication

`tax-engine-web` (when built) talks to `tax-engine-api` over REST/JSON: a single
`POST` with the contributor's attributes, returning the full calculation breakdown
as JSON. No other communication path exists or is planned.

---

## DevOps

Not yet implemented. Containerizing `tax-engine-api` with Docker is listed under
"Development Order" as a stretch goal, not part of the mandatory MVP. The root
`.github/workflows/ci.yml` inert placeholder is unchanged for now — wire up a real
pipeline only once there's a build to run.

---

## Development Order

| Phase | Subprojects | Goal | Status |
|-------|-------------|------|--------|
| 1 | tax-engine-api | Stateless calculation MVP: Controller + Service + bracket engine + unit tests proving each calculation step | In progress |
| 2 (stretch, time-permitting) | tax-engine-api, tax-engine-web | Persistence (entities, repository, id-exists check, save), unstyled frontend form, Docker, Update Record | Planned |

---

*2026-08-17 — Overseer: replaced the blank template with the Tax Calculator Engine's
real architecture, business rules, and 2025 federal tax data, per the spec provided
by the user. Subprojects renamed from the template's `backend`/`frontend`
placeholders to `tax-engine-api`/`tax-engine-web` (see root `CLAUDE.md` changelog for
the corresponding rename).*
