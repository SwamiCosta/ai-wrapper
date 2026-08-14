# Skill 13 — Ask Before Inferring

**Scope:** Any agent, at any point in a task, when progress depends on a fact that only the user can authoritatively provide.

### The problem this solves

Skill 10 (Investigation Hygiene) governs *how* to investigate efficiently once investigation is the right move. It does not address a different failure mode: spending multiple rounds of code-reading, history search, or speculative inference trying to recover a fact that isn't actually derivable from the repository at all — a business-rule decision, a scope boundary, a missing spec value, a product priority — when the only correct source for that fact is the user. The cost isn't just tokens; it's the risk of silently inferring the wrong answer and building on it.

### Rule

Before spending more than one investigation step on a question, classify it:

- **Derivable** — the answer exists in code, Git history, configuration, or documentation, and a narrow lookup (per Skill 10) can recover it. Investigate.
- **Not derivable** — the answer is a decision, preference, or fact only the user holds (never written down, or what's written down is ambiguous/contradictory and the agent can't tell which version is current). Ask immediately, in a single direct question, instead of guessing or searching further.

A useful test: if the same question, asked to the user directly, would take them ten seconds to answer, it almost never justifies multiple rounds of inference instead.

### What this looks like in practice

- A business rule is ambiguous between two existing docs → ask which one is current, don't guess and proceed.
- A spec is missing a value needed to implement it (a threshold, a default) → ask for the value, don't pick a plausible one silently.
- The scope of a request is unclear ("update the labels" — which labels, which subproject) → ask one focused clarifying question before starting work that might be discarded.
- A brand-new project has no `ARCHITECTURE.md` content yet and a task depends on a decision that belongs there → ask, and suggest the answer get recorded in `ARCHITECTURE.md` so it doesn't have to be asked again.

### What this does not require

- It doesn't block ordinary, narrow lookups that are part of normal task execution.
- It doesn't override Skill 10 — when investigation is the right move, that skill still governs how to do it efficiently.
- It doesn't require asking about things already settled in `CLAUDE.md`, `ARCHITECTURE.md`, or an agent's own file — those are already the user's authoritative answer, recorded once so it doesn't have to be asked again.

### Rationale

An agent that infers instead of asking can produce work that looks complete but is built on a wrong assumption — discovered only after the fact, costing more than the question would have. Asking early is cheaper than investigating long and being wrong.
