# Skill 08 — Repository Sync

**Scope:** Every agent, in every repository it is about to act in, before starting the task itself.

### The rule

Before reading project source or task-specific documentation, implementing anything, creating a branch, running commands, or opening a PR — synchronize the repository:

1. `git checkout main`
2. `git pull`

This does not apply to an agent's own fixed mandatory reading (`ARCHITECTURE.md`, `CLAUDE.md`, `SKILLS.md`, a subproject `CLAUDE.md`, the agent's own `.md` file, and any Always-tier skill) — reading those is how an agent learns this very rule in the first place, not an action taken on the project's current state. Sync once the agent moves on to the actual task.

If a task spans multiple repositories, apply this to each one before starting work in it. A second pull is not required for the same repository within the same task session — but see Skill 09 if you're resuming work on a branch that already has an open PR; that's a different check with a different trigger.

### Rationale

Work started on a stale checkout can duplicate changes someone else already merged, silently conflict with recent commits, or produce an assessment (a code review, an investigation) that no longer reflects what's actually in `main`. The cost of syncing first is a few seconds; the cost of not syncing is discovering the mistake after work is already built on top of it.
