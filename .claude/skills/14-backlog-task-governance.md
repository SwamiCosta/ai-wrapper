# Skill 14 — Backlog Task Governance

**Scope:** Any interaction with whatever backlog/task-tracking tool this project uses — Trello, Jira, Linear, GitHub Projects, or a plain markdown file, once one is chosen (see `CLAUDE.md` — Getting started).

### Read-open, write-gated

Any agent may read the backlog at any time — checking what's planned, what's in progress, what a task's description says. Writing to it (creating a card/issue, moving it between states, updating its status or description) requires the user's explicit request in that session. An agent does not proactively reorganize the backlog as a side effect of finishing a task, even if the update looks obviously correct.

### Stable task IDs

If the tool supports it, use a stable, project-prefixed identifier for each task (e.g. `backend-14`, `frontend-3`) and never reuse an ID once assigned, even if the original task is deleted or abandoned. This keeps references in commit messages, PR descriptions, and agent handoff reports meaningful over time.

### Definition of done, stated before the task starts

A task isn't "started" until its acceptance criteria are written down — what tests must pass, what documentation must update, what a reviewer checks before calling it complete. This is not bureaucracy for its own sake: it's the concrete artifact that prevents scope drift discovered only after a PR is already open, and it gives the architect (Skill 04) something specific to check the PR against rather than an impression of whether it "looks right." Record it in the task's own description in whatever tool you're using; if the task is small enough that writing this down feels like overkill, that's a signal the task is small enough to just do without opening a formal backlog entry at all.

### Rationale

The read/write split matches this project's default posture on protected, shared-state changes: cheap and reversible reads happen freely, but coordinated changes only happen when the user has actually asked for them in this session. The backlog is shared, human-facing state — the same category as the branches and PRs this project's other rules already protect.
