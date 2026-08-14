# Skill 04 — Architect Review

**Scope:** Any pull request opened by a Level 1 or Level 2 agent, in any subproject with a resident Level 3 architect.

### The rule

A PR from a developer or scribe agent is reviewed by that subproject's Level 3 architect (or Overseer, for root-level PRs) before it is presented to the user as ready. The architect may approve it for user review, request changes, or reject it — but never merge it; that authority stays with the user regardless of level.

This review step exists specifically to keep authorship and review separate — the same reason Skill 03 separates feature authorship from test authorship. An agent reviewing its own PR under a different persona in the same session collapses that separation; if you're ever tempted to do that, stop and flag it to the user rather than treat it as routine (see Skill 11's note on this).

### What the architect checks

- Does the change match what the task actually asked for (see Skill 16 — scope discipline)?
- Is test coverage present per Skill 03's baseline?
- Does any hand-maintained reference or mirror document need a matching update in the same PR (Skill 06 — opened together with this skill)?
- Does the change touch a protected action without the authorization to match?
- Does the change introduce an unexplained deviation from this project's documented architecture?

### Rationale

A second set of eyes catches what the author's own context made invisible to them — not because the author was careless, but because anyone deep in the implementation of a change loses some ability to see it the way a fresh reader will. Keeping that second look structurally separate, rather than optional or self-administered, is what makes it reliable.
