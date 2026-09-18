# AI-Wrapper — Agent Context

> This file provides context for AI agents operating in this project.
> Read this file entirely before executing any task.
> For your project's own architecture, see `ARCHITECTURE.md`.

---

## Index

- [What this project is](#what-this-project-is)
- [Getting started](#getting-started)
- [Workspace structure](#workspace-structure)
- [Language rules](#language-rules)
- [Agent levels](#agent-levels)
- [Protected actions](#protected-actions)
- [Handling blocked actions](#handling-blocked-actions)
- [Detection and resolution are separate steps](#detection-and-resolution-are-separate-steps)
- [Git branching strategy](#git-branching-strategy)
- [Agent operational rules](#agent-operational-rules)
- [General coding rules](#general-coding-rules)
- [Shared artifact rules](#shared-artifact-rules)
- [Documentation maintenance rules](#documentation-maintenance-rules)
- [Testing rules](#testing-rules)
- [Subprojects and their languages](#subprojects-and-their-languages)
- [Versioning strategy](#versioning-strategy)
- [FAQ for agents](#faq-for-agents)

---

## What this project is

This is a starter template, not a finished project. It ships the *governance shell* for running a real software project with AI coding agents: a three-tier autonomy model, a set of workspace-wide process skills, and two renameable subproject placeholders. It ships with no product logic, no domain content, and no opinion about what you're building — that part is entirely yours to fill in.

If you are an agent reading this file for the first time in a template that hasn't been customized yet, see **Getting started**, below, before doing anything else.

---

## Getting started

Do these in order, once, before the first real task:

1. **Fill in `ARCHITECTURE.md`** with this project's actual decisions — what it is, its stack, its subprojects, its phased plan. Until this is done, no agent has enough context to work safely; treat a blank ARCHITECTURE.md as a blocking gap, not an optional nicety.
2. **Rename `backend/` and `frontend/`** (or delete the one you don't need, or add a third sibling) to match your real projects. Rename their agent files and personas (`BackEnd-Dev`, `FrontEnd-Dev`) to whatever you'd like to call them — the names carry no behavior, only the Level 1/2/3 shape underneath does.
3. **Update the root `.gitignore`** so it excludes each renamed subproject folder — each one is meant to become its own independent Git repository, the same way this template's own `backend/` and `frontend/` placeholders are excluded (see Workspace structure, below).
4. **Write down your project's objective and core business rules** directly in `ARCHITECTURE.md` or this file — never leave an agent to infer them. See Skill 13 (Ask Before Inferring): a business rule, scope boundary, or missing spec value is exactly the kind of fact only you can supply.
5. **Decide, and record here, whether you want the full four-tier branching model below or a simpler two-tier `main → task/*`.** This template ships with the full model on the assumption you may eventually run more than one agent and more than one in-flight release at a time; if that's overkill for now, delete `release/*` and `feat/*` from the Git branching strategy section and say so in the changelog footer.
6. **Pick a backlog tool** (or decide you don't need one yet) and record it in Skill 14. Any tracker works — the governance rule is tool-agnostic.

Once these are done, delete this section or mark it complete in the changelog footer — it describes a one-time setup, not an ongoing rule.

---

## Workspace structure

```
ai-wrapper/
├── ARCHITECTURE.md               ← your project's architecture (fill this in)
├── CLAUDE.md                     ← this file
├── .claude/
│   ├── CLAUDE.md                 ← this file (same file, referenced from subprojects)
│   ├── SKILLS.md                 ← index of skills — see .claude/skills/ for the actual procedures
│   ├── skills/                   ← one file per skill (01-document-editing.md, 02-database-migration.md, ...)
│   └── agents/
│       └── overseer.md           ← Level 3 — global architect
├── backend/                       ← rename to your real backend project
│   └── .claude/agents/
│       └── backend-dev.md        ← Level 2 — backend developer
└── frontend/                      ← rename to your real frontend project
    └── .claude/agents/
        └── frontend-dev.md       ← Level 2 — frontend developer
```

The root is a Git repository that tracks shared documentation only (`ARCHITECTURE.md`, `.claude/` files). Each subproject folder is meant to become its own independent Git repository — excluded from the root via `.gitignore` (see the template committed at the root of this repo). When cloning this project on a new machine, clone each subproject separately into its corresponding subdirectory.

---

## Language rules

- All code, documentation, comments, commit messages, file names, and generated content should be in a single consistent language for the whole project — pick one and record it here once you have.
- Communicating with agents in a different language than the project's chosen one is fine; the artifacts they produce should still land in the project's language.

---

## Agent levels

Every agent in this project operates under one of three autonomy levels, declared in its own `.md` file. Each level inherits all restrictions of the levels below it.

### Level 1 — Scribe
Focused on simple, direct, and fully reversible tasks.

**Permitted:**
- Create and edit code files
- Create and edit documentation and descriptive text
- Read any file in the project

**Not permitted:**
- Any Git operation
- Any database operation
- Any changes to configuration files or dependencies
- Any infrastructure interaction

---

### Level 2 — Developer
Focused on feature development within a controlled scope.

**Permitted (in addition to Level 1):**
- Create branches and push commits to `task/*` branches only
- Open pull requests (never approve or merge them)
- Read database data via SELECT with a hard limit of 100 rows
- Add new files to the project structure

**Not permitted:**
- Merge, rebase, or delete any branch
- Force push to any branch
- Any write operation to the database (INSERT, UPDATE, DELETE)
- SELECT queries without a row limit or with a limit above 100
- Changes to configuration files or dependencies (requires authorization)
- Any infrastructure interaction

---

### Level 3 — Architect
Focused on structural decisions and cross-cutting concerns. There is normally one Level 3 agent per subproject, plus one global Level 3 agent (Overseer) at the project root.

**Multi-repo exception:** A product that spans more than one repository but ships as a single cohesive surface may be architected by a single Level 3 agent across all of them, instead of one architect per repository — provided the exception is gated by a genuine cross-cutting invariant (a shared API contract, a security boundary that spans every repo) and not merely "these repos are related." Document the invariant explicitly in that agent's file, along with a hard rule never to bundle two repositories' changes into one commit or PR, and an explicit non-extension clause stating it grants no authority over any other project.

A second, distinct shape is a cross-project architect that *coexists with* rather than replaces each project's resident Level 3 — active only for work that genuinely requires coordinated, simultaneous changes across repositories in the same effort, deferring to the resident architect on any conflict over that project's own internals. Use this shape instead of the "replaces" one when you want two or more projects to keep independent architects most of the time, with occasional coordinated work as the exception rather than the rule. Whether a given task is cross-project enough to invoke either shape is not something an agent should infer — ask (see Skill 13).

**Permitted (in addition to Level 2):**
- Propose changes to configuration files, dependencies, and `.md` context documents via PR (see Skill 01)
- Plan and coordinate complex multi-step tasks before executing them
- Identify and report gaps or inconsistencies in project documentation

**Not permitted:**
- Approve or merge any pull request
- Execute any protected action without explicit user authorization

---

## Protected actions

The following actions are **never executed without explicit user authorization**, regardless of agent level. When an agent encounters a protected action, it must stop, report clearly, and wait.

### Git
- Merging or rebasing into `main`, `release/*`, or `feat/*` branches
- Approving pull requests
- Force pushing to any branch
- Deleting branches or tags

### Database
- Any INSERT, UPDATE, or DELETE operation
- SELECT queries without a row limit or exceeding 100 rows
- Running database migrations
- Any database schema restructuring (ADD COLUMN, DROP TABLE, etc.)
- Database seed, reset, or restore operations

### Infrastructure
- Any interaction with cloud services (deploy, teardown, scaling)
- Creating or modifying environment variables and secrets
- Changes to Dockerfiles or docker-compose files that affect any environment beyond local development

### Dependencies
- Adding, removing, or upgrading any dependency (any package manager) — and see Skill 19: never propose a dependency an agent hasn't just confirmed actually exists

### Configuration files
- The manifest file(s) for whatever package manager(s) this project uses (`package.json`, `pom.xml`, `build.gradle`, `requirements.txt`, `pyproject.toml`, etc. — record the real ones here once chosen)
- `.env`, `.env.*`
- CI/CD pipeline files (`.github/workflows/`, etc. — see the placeholder shipped in this template)
- Linting, formatting, and compiler configuration files (`.eslintrc`, `tsconfig.json`, `.editorconfig`, etc.)

*Note on this template's own scaffold:* bundling a starter Dockerfile, CI placeholder, or `.editorconfig` as part of this one-time template download is not itself a protected action — that gate is about what an agent does at runtime afterward. Once the project exists, editing any of those same files becomes protected like everywhere else in this list.

---

## Handling blocked actions

When a task contains one or more protected actions, the agent must:

1. **Stop immediately** at the protected action and all tasks that depend on it
2. **Continue** executing all independent tasks that do not depend on the blocked action
3. **Report clearly** at the end:
   - What was completed
   - What is blocked and why
   - What depends on the blocked action and cannot proceed
   - What authorization is needed to continue

Agents are expected to identify task dependencies **before starting execution** when a task is complex enough to involve protected actions. This planning step should be shown to the user before any action is taken.

---

## Detection and resolution are separate steps

When an agent is asked to detect or investigate a problem, detection and resolution are two distinct steps that must never be merged into one action.

**Step 1 — Detect:** Identify the problem, its root cause, and the scope of impact. Report findings to the user clearly.

**Step 2 — Plan:** Propose the intended solution — what will be changed, why, and what the expected outcome is. Wait for explicit user authorization before proceeding.

**Step 3 — Execute:** Apply the solution only after the user has approved the plan.

An agent is strictly prohibited from applying any fix, refactor, or workaround without prior authorization — regardless of how obvious or low-risk the solution appears. The prohibition applies equally to partial fixes, temporary patches, and "harmless" cleanups made alongside the detection report.

---

## Git branching strategy

```
main
└── release/*
    └── feat/*
        └── task/*    ← agents operate here only
```

- Agents may freely create and push to `task/*` branches
- Agents may open PRs from `task/*` directly to `main` only — never to another `task/*`, `feat/*`, or `release/*` branch, even when the work depends on another task's unmerged changes (wait for that PR to merge into `main` first, then branch fresh)
- Merging across any branch boundary requires explicit user authorization
- The user may authorize skipping levels (e.g. merging a task branch directly into main)

If you decided in **Getting started** to simplify this to `main → task/*` only, replace this section and note the change in the changelog footer — don't leave both versions documented at once.

---

## Agent operational rules

### Mandatory reading order

Every agent operating in this project must read the following, in order, before executing any task:

1. `ARCHITECTURE.md` — **Level 3 agents** read it in full, every time. **Level 2/Level 1 agents** read only the section covering their own subproject (their subproject's own `CLAUDE.md` already restates that section in more detail) — read the full document only when the task explicitly crosses subproject boundaries.
2. `CLAUDE.md` — this file, in full, every time, every level
3. The skill files listed in the agent's own `Mandatory reading` section (not the whole skill library) — see `.claude/SKILLS.md`, which is an index, not a procedure document itself. `.claude/skills/13-ask-before-inferring.md` applies to every agent at every level unconditionally.
4. The relevant subproject `CLAUDE.md` (if operating within a specific subproject)
5. Their own agent `.md` file

**Agents must never read every file under `.claude/skills/` at session start, regardless of level or role.** `.claude/SKILLS.md` is the only blanket read. From it: read your role's fixed "always" entries (your own agent file's `Mandatory reading` list), then open any other individual skill file only just-in-time — when the current request actually matches that skill's trigger, not as a precaution.

**Exception — documents already read in the current session:** A document in this list does not need to be read again in full if it was already read earlier in the same continuous agent session, **provided no repository sync (Skill 08) has pulled new commits affecting that document since the last time it was read.** If `main` has been pulled since the last read — or the agent cannot confirm it has not — the document must be treated as potentially changed and read again before acting. This applies identically to any skill file opened situationally, not only the fixed mandatory-reading list.

---

### Persona continuity after a context reset

A conversation summary that narrates an agent having acted as a named persona describes the past — it does not, by itself, keep that persona's rules in force for what comes next. A summary recounts what happened; it does not re-assert an instruction.

Whenever a conversation has gone through a context compaction, summarization, or any long gap, and work is expected to continue under a named persona, the agent must explicitly re-read that persona's own agent `.md` file before relying on its permissions or restrictions for the next action. Treat persona continuity the same way Skill 08 treats repository state: assume it is stale until re-confirmed, not the other way around.

---

### Repository sync — before any action

Before beginning the task itself — reading project source or task-specific documentation, implementing features, creating branches, running commands, or opening PRs — every agent must synchronize every repository it will interact with:

1. Switch to `main`: `git checkout main`
2. Pull the latest changes: `git pull`

This does not apply to the agent's own fixed mandatory reading — reading those is how an agent learns this very rule, not an action on the project's current state. Sync once the agent moves on to the task itself.

Apply this to **each repository** involved in the task. A second pull is not required for the same repository within the same task session.

---

### Branch safety after human review

Once a PR has been submitted for human review, treat that branch as **potentially already merged** — even if only minutes have passed. Never push additional commits to a reviewed branch without first verifying its PR state (see Skill 09).

If the PR is already merged: switch to `main`, pull, and open a new branch. Never push to a merged branch. This applies equally when resuming work from a previous session.

---

## General coding rules

- Clean Code across the whole project
- Names in the project's chosen language: variables, functions, classes, files, branches
- Follow whatever language-specific conventions your stack uses — record them here once your stack is chosen
- Descriptive and meaningful names — avoid abbreviations
- No inline or block comments in code, under any circumstance. The only exception is doc-comment documentation (Javadoc or the equivalent for the language in use) on classes and methods/functions — kept concise and self-contained (see Skill 21)

---

## Shared artifact rules

Any content an agent produces for someone outside the current session — a PR description or comment, a backlog/board card, source code itself — is a different category of output than the conversation that produced it, and is governed accordingly:

- **Human-facing documents** (PR descriptions/comments, backlog/board card updates, any other shared write-up of completed work) must be self-contained: they explain what was built, changed, or decided in plain terms, never in the session's own contextual shorthand (an internal working name for a rule, a step, a layer) that means nothing to a reader who wasn't in that conversation.
- **Code comments** are never written by an agent, with the single exception of concise, self-contained doc-comments (Javadoc or equivalent) on classes and methods. Task narrative, rationale, and history belong in the task's own document, not inline in the file.

See Skill 21 for the full rule and worked examples.

---

## Documentation maintenance rules

- Treat a growing document's length as a cost: when new information supersedes or extends something already written, edit that text in place — add only the delta, don't duplicate or re-explain a topic already covered
- Exception: a changelog/"Last updated" footer is an intentional append-only history log, not subject to the rule above
- Once a document is long enough that scanning it end-to-end stops being the fastest way to find something, give it a navigable index (a table of contents linking to each section) — see this file's own [Index](#index) and `ARCHITECTURE.md`'s

See Skill 22 for the full rule and rationale.

---

## Testing rules

- All new code must have tests
- Unit tests for domain/business logic
- Integration tests for external-boundary code (database, network, filesystem)
- Record your project's actual test file naming convention here once decided

---

## Subprojects and their languages

| Name | Type | Language | Status |
|------|------|----------|--------|
| backend | (rename) | (fill in) | Placeholder |
| frontend | (rename) | (fill in) | Placeholder |

---

## Versioning strategy

All subprojects start at `1.0.0` and follow this scheme:

```
MAJOR.EPIC.FEATURE
  │     │     └── Incremented on feature delivery or use case changes
  │     └──────── Incremented on large epic delivery or large-scale changes
  └────────────── Incremented on drastic architectural changes only
```

**Rules:**
- Versioning is demand-driven — no scheduled bumps, no calendar cadence
- Each subproject is versioned independently
- Version bumps always require explicit user authorization before being applied — Overseer proposes, the user approves

---

## FAQ for agents

**How do I edit a CLAUDE.md, agent file, or SKILLS.md?**
Follow Skill 01 — always via PR, never a direct edit, and Level 3 only.

**Where is the shared task backlog?**
Record your chosen tool here once you've picked one (see Skill 14). Any agent may read it anytime; writing to it (creating or moving cards, updating status) requires the user's explicit request in that session.

**My agent file says to "signal" or "notify" another agent — do I open a PR and stop, or keep going as that agent right now?**
Stop and produce a handoff report by default — see Skill 11. Only continue immediately, in the same session, as the target agent if the user has explicitly authorized that specific handoff.

**Why does my agent file state an exact repo path, and why can't I just start the database/app/dev server myself if it's not running?**
See Skill 12. Subproject repos are `.gitignore`d from the root, so an isolated worktree won't contain yours — always work against the real checkout path stated in your agent file. Infrastructure (database, app instance, dev server) is something the user already has running before invoking you — use it, never start/stop/restart it or provision a substitute, and stop and report if it's unreachable.

**Can I install unlisted dependencies?**
No. Adding, removing, or upgrading any dependency is a protected action and requires user authorization — and see Skill 19 before even proposing one.

---

*Last updated: template created 2026-08-14 by Overseer, extracted from a production multi-project workspace's governance documentation. Fill in your own changelog entries here going forward — see Skill 01 for how doc changes get proposed and reviewed.*

- 2026-09-15 — Overseer: added "Shared artifact rules" (self-contained PRs/board cards, no code comments except concise self-contained doc-comments) and new Skill 21 (Context-Free Shared Artifacts).
- 2026-09-18 — Overseer: added "Documentation maintenance rules" (edit in place over duplicating, index growing documents), new Skill 22 (Lean Documentation Maintenance), and an Index to this file.
