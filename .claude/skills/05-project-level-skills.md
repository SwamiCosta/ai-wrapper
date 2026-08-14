# Skill 05 — Project-Level Skills

**Scope:** Any subproject that requires domain-specific, stack-specific, or convention-specific procedures too narrow to belong in this root `SKILLS.md`.

### The pattern

Subprojects may define their own local skills under `<subproject>/.claude/skills/`. Each file is a self-contained procedure in Markdown, named descriptively (e.g. `orm-adapter-checklist.md`, `component-testing-standard.md`).

### Project skill vs. root skill

| Use a **project skill** when | Use a **root skill** when |
|---|---|
| The procedure applies to one subproject's stack or conventions | The procedure applies across two or more subprojects |
| The rule references framework-specific detail (a specific ORM, a specific test runner) | The rule defines agent behavior, role separation, or workflow governance |
| The guidance wouldn't transfer meaningfully to another subproject | The guidance is valid regardless of which subproject an agent is working in |

When in doubt: if the skill names a specific framework or tool that isn't used in more than one subproject, it belongs at the subproject level.

### Standard procedure

1. Create the skill file at `<subproject>/.claude/skills/<descriptive-name>.md`.
2. Open the relevant agent `.md` file(s) — Mandatory reading section or a Behavior section — and add a reference to the new skill file. A project skill that no agent file references will never actually be read.
3. That subproject's Level 3 architect (or Overseer, if the subproject has none yet) is responsible for maintaining its project skills and keeping the references current.
4. Changes to an existing project skill file follow Skill 01 — they live in a Git repository, and editing them is a document edit like any other.

### Mandatory referencing rule

A project skill has no effect if no agent knows it exists. Every project skill must be referenced in at least one agent file, with the full relative path and a one-line description:

```
- `.claude/skills/orm-adapter-checklist.md` — import conflict checks and layer boundary rules for ORM adapters
```

### Rationale

Root-level skills define universal behaviors that hold regardless of stack. Subproject skills capture the accumulated knowledge of a specific codebase — naming collisions, framework quirks, test patterns — that agents need to avoid rediscovering the same pitfall on every task. Keeping them file-per-subproject, referenced explicitly, makes them findable without bloating the root skill set with content that only ever applies to one stack.
