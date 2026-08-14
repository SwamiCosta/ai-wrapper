# AI-Wrapper

A generic starter kit for running software projects with AI coding agents under a defined autonomy hierarchy — extracted from a production multi-project workspace, stripped of anything specific to that workspace's own product or stack.

This file is intentionally short. The real source of truth is:

- **[`CLAUDE.md`](./CLAUDE.md)** — the rules every agent operates under: autonomy levels, protected actions, branching strategy, and the first-run checklist for setting this template up as your own project.
- **[`ARCHITECTURE.md`](./ARCHITECTURE.md)** — a blank template for your project's own architecture. Fill this in before doing anything else; see CLAUDE.md's "Getting started" section.

Do not duplicate content from those two files here — if this README and CLAUDE.md ever disagree, CLAUDE.md wins, and the disagreement is a bug to fix, not a choice to make.

## What's in this template

```
ai-wrapper/
├── CLAUDE.md, ARCHITECTURE.md   ← read these first
├── .claude/
│   ├── SKILLS.md                ← index of the 20 workspace-wide skills
│   ├── skills/                  ← one file per skill
│   └── agents/overseer.md       ← Level 3 global architect
├── backend/                     ← rename this to your real backend project
│   └── .claude/agents/backend-dev.md   ← Level 2
└── frontend/                    ← rename this to your real frontend project
    └── .claude/agents/frontend-dev.md  ← Level 2
```

`backend/` and `frontend/` are placeholders, not a mandated shape — rename them, drop the one you don't need, or add more sibling projects the same way. See `.claude/skills/05-project-level-skills.md`.
