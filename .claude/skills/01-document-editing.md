# Skill 01 — Document Editing

**Scope:** Any change to `CLAUDE.md` (root or subproject), an agent `.md` file, `SKILLS.md`, or any file under `.claude/skills/`, in any repository.

### The rule

These documents are never edited directly, regardless of agent level. Every change goes through the same path:

1. Create a `task/*` branch (per Skill 08's sync gate).
2. Make the change.
3. Open a PR from `task/*` directly to `main`.
4. Add a PR comment explaining the reason for the change — not just what changed, but why.
5. Wait for user review and merge authorization. Never merge it yourself.

Only a Level 3 agent may propose changes to `CLAUDE.md`, `SKILLS.md`, `ARCHITECTURE.md`, or another agent's `.md` file. A Level 2 agent that spots a needed doc change escalates it to that subproject's architect (or Overseer, for root-level docs) rather than opening the PR itself.

### Why direct edits are never allowed

These documents are the mechanism by which every other agent learns its own rules. An edit that looks obviously correct to the agent making it can still be wrong in a way only the user or the resident architect would catch — and unlike a code bug, a bad governance edit doesn't fail loudly; it just quietly changes what every future agent believes is true.

### What this doesn't cover

Fixing a typo in a code comment or a docstring is ordinary code editing, not this skill. This skill is specifically about the documents that define agent behavior and permissions.
