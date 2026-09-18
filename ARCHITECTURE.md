# [Project Name] — Architecture

> Central document for this project. Read before starting any task.
> This file ships blank on purpose — see `CLAUDE.md` → "Getting started."
> A Level 3 agent (Overseer) should treat a blank section here as a blocking
> gap, not something to infer or fill in with a plausible guess (Skill 13).

---

## Index

- [Overview](#overview)
- [Subprojects](#subprojects)
- [backend / frontend](#backend-rename-this-section-to-match) *(rename this entry and the anchors below to match once the subprojects are renamed — see `CLAUDE.md` → "Getting started")*
- [Business rules and domain](#business-rules-and-domain)
- [Architectural Principles](#architectural-principles)
- [Inter-service Communication](#inter-service-communication)
- [DevOps](#devops)
- [Development Order](#development-order)

---

## Overview

*One paragraph: what is this project, who is it for, and what does it do. Replace this placeholder before any agent starts real work.*

---

## Subprojects

| Name | Type | Language | Status |
|------|------|----------|--------|
| backend | *(rename)* | *(fill in)* | Placeholder |
| frontend | *(rename)* | *(fill in)* | Placeholder |

---

## backend *(rename this section to match)*

### Responsibilities
*What does this subproject own? What does it explicitly not own?*

### Architecture
*Layering approach, key patterns, database, framework — whatever is actually decided. Don't describe a pattern here that isn't actually enforced; if you want hexagonal architecture, ports & adapters, or any other layering discipline, say so explicitly and record it in `backend/CLAUDE.md`'s "Architecture rules" section too, since that's what BackEnd-Dev actually reads day to day.*

---

## frontend *(rename this section to match)*

### Responsibilities
*What does this subproject render, and what does it consume from elsewhere?*

### Stack
*Framework, build tool, styling approach, state management choice.*

---

## Business rules and domain

*This is the section most worth not leaving blank. Domain entities, core business rules, invariants that must never be violated, terminology specific to your product. An agent that has to guess a business rule from code alone will eventually guess wrong — write it here instead (Skill 13).*

---

## Architectural Principles

*Why did you choose the architecture above? What tradeoffs did you accept on purpose? A principle stated here saves a future agent from re-litigating a decision that's already made.*

---

## Inter-service Communication

*How do your subprojects talk to each other, if at all? REST, WebSocket, events/queues, or nothing yet because there's only one subproject so far.*

---

## DevOps

*Containerization, CI/CD, deployment targets — to be filled in as they're decided. This template ships a Dockerfile placeholder per subproject and a CI workflow placeholder at `.github/workflows/` — replace both with your real pipeline rather than leaving the placeholders as the actual answer.*

---

## Development Order

| Phase | Subprojects | Goal | Status |
|-------|-------------|------|--------|
| 1 | | | Planned |

---

- 2026-09-18 — Overseer: added an Index section for navigation (Skill 22).

*Last updated: template created 2026-08-14. Replace this changelog with your own project's history going forward — one entry per structural decision, newest first, per Skill 06.*
