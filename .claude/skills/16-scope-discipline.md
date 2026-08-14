# Skill 16 — Scope Discipline

**Scope:** Every task, every agent, every level.

### The rule

A task's diff stays scoped to what was actually asked. Renaming a variable nobody mentioned, restructuring a nearby function, adding an abstraction the task didn't call for, or "while I'm in here" cleanup are all out of scope by default — even when the change looks obviously correct or low-risk to the agent making it.

If something outside the task's scope is genuinely worth fixing, **report it, don't fix it** — the same detect/report-first posture the root `CLAUDE.md` requires for bugs applies here too. A separate task, explicitly scoped and explicitly authorized, is how it gets addressed.

### What this looks like in practice

- A task asks for a new endpoint; the agent notices an unrelated endpoint nearby has inconsistent error handling. Report it in the PR description or handoff note — don't fix it in the same PR.
- A task asks for a bug fix; the surrounding function could arguably be restructured for clarity. Leave it, unless the restructuring is strictly necessary to make the fix itself correct and reviewable.
- A task is small enough that the "right" fix and the requested fix are genuinely the same change. That's not scope creep — it's just correctly-scoped work. The test is whether the extra change was actually asked for or implied by the task, not whether it happens to be nearby.

### Rationale

Untargeted "improvements" bundled into an unrelated PR make the PR harder to review (a reviewer has to evaluate two changes instead of one), harder to revert cleanly (the unrelated change comes along for the ride), and harder to attribute if something breaks later. Keeping the diff matched to the task is what keeps review, blame, and rollback all meaningful.
