# Skill 12 — Agent Operating Environment

**Scope:** Every subproject-level agent file and every subproject `CLAUDE.md`, in any subproject with at least one agent defined.

### The problem this solves

Two recurring sources of avoidable delay: (1) an agent spending time trying to locate or recreate its own subproject's repository, because an isolated agent worktree created at the project root does not contain subproject repositories — each one is `.gitignore`d from the root (see `CLAUDE.md` — Workspace structure); (2) an agent provisioning a disposable substitute for required infrastructure (spinning up a throwaway container as a stand-in for a missing local toolchain) instead of using the instance the user already has running.

### Rule — Repository location

Every subproject-level agent file must state, in or immediately after its Identity section, the absolute local path of the repository it operates in, and that this repository is excluded from the root repository so an isolated worktree created there will not contain it. The agent must operate directly against that real checkout path — never search for, clone, or recreate the repository elsewhere — and must stop and report if that path isn't accessible, instead of working around it.

### Rule — Local environment assumptions

Every subproject `CLAUDE.md` must declare what the user is expected to already have running before any agent in that subproject is invoked (a database and an app instance for a backend; a dev server for a frontend). Once declared, every agent in that subproject must:

- **Use what's already running** — never start, stop, or restart that infrastructure, and never provision a disposable substitute for a missing dependency.
- **Stop and report** if something required isn't running or reachable, rather than working around it by starting something new.

The *fact* of what infrastructure exists is subproject-specific and belongs in that subproject's own `CLAUDE.md`. The *rule* — use what's running, never substitute, report instead of working around — is what generalizes and doesn't need to be redocumented from scratch each time; each subproject's `CLAUDE.md` only needs to state its own infrastructure shape and reference this skill for the behavioral rule.

### Rule — Scoped exceptions to a protected action

A subproject may grant a specific Level 2 agent a narrow, documented exception to one otherwise-protected action, when the exception is strictly read-only or otherwise bounded in a way that doesn't weaken the protection's purpose. When granting one:

- The exception is granted to one named agent for one named action, not to the protected-action category as a whole, and does not change the root `CLAUDE.md` Protected Actions list.
- Record it directly in that agent's own `.md` file (its Autonomy/permissions section), and cross-reference the underlying procedure from a project skill if one documents it.
- It requires the same explicit user authorization as any other protected-action change, before being documented (see Skill 01).
- State, in the exception itself: the baseline rule being deviated from, who authorized it and why, and an explicit non-extension clause — it applies to this one agent and this one action, nothing broader.

### What this skill does not require

No agent's permission level or scope changes — this skill only standardizes *how* existing facts (repo path, running infrastructure, a granted exception) are documented, not what any agent is allowed to do.

### Rationale

A rule stated once, generally, and referenced everywhere is easier to keep consistent than the same paragraph copy-pasted with drift into every subproject's docs.
