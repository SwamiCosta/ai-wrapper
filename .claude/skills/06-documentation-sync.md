# Skill 06 — Documentation Sync & Freshness

**Scope:** Any code change that has a hand-maintained reference or mirror document describing it, and any periodic check of whether project documentation still matches reality.

### Same-delta sync

A hand-maintained reference doc — a formulas page, a domain-entity reference, an API contract summary living outside the code that implements it — isn't complete until it's updated in the *same* PR as the source change it mirrors. The reviewing architect (Skill 04, opened together with this skill) checks this explicitly during review; it is not left to the author's memory or to a later cleanup pass that may never come.

### Periodic freshness audits, beyond what any single PR catches

Per-PR sync catches drift introduced by the PR being reviewed. It does not catch drift that accumulates silently in documents nobody's PR happens to touch — a `CLAUDE.md` "Local environment assumptions" section describing infrastructure that changed for unrelated reasons, an `ARCHITECTURE.md` phase table nobody updated after a phase actually finished. The resident architect (or Overseer, workspace-wide) should periodically spot-check root and subproject documents against actual current state — not on every task, but as a standing responsibility rather than something that only happens when a PR happens to surface it.

A stale reference document that's never corrected doesn't just sit there harmlessly — every agent that reads it as authoritative builds on a wrong assumption. If you find one, report it per the Detect → Plan → Execute split in the root `CLAUDE.md` rather than silently correcting it inline as a side effect of unrelated work.

### Rationale

Documentation that describes code drifts from it constantly by default — the code changes are the ones anyone notices; the doc changes are opt-in unless something makes them mandatory. Tying the update to the same delta removes the "I'll do it later" gap for changes made through a PR; the periodic audit exists because not every drift is introduced through a PR at all.
