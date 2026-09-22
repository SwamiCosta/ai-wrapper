# Keynor Workspace — Architecture

> Central document for this project. Read before starting any task.
> Migrated from the original `keynor-workspace` governance system into the
> `ai-wrapper` shell — see `CLAUDE.md` for the project-wide rules this
> document assumes.

---

## Index

- [Overview](#overview)
- [Subprojects](#subprojects)
- [aniannoth-overview](#aniannoth-overview)
- [keynor-core](#keynor-core)
- [summon-unity](#summon-unity)
- [Business rules and domain](#business-rules-and-domain)
- [Architectural Principles](#architectural-principles)
- [Inter-service Communication](#inter-service-communication)
- [Testing strategy](#testing-strategy)
- [DevOps](#devops)
- [Development Order](#development-order)

---

## Overview

The Keynor Workspace is an ecosystem of applications built around an original fantasy/fiction universe. The goal is to transform accumulated stories into literary narratives, game systems, and interactive experiences — while serving as a learning environment for software architecture, AI-assisted development, and DevOps practices.

---

## Subprojects

| Name | Type | Language | Status |
|------|------|----------|--------|
| aniannoth-overview | Frontend | TypeScript / React + Vite | In Development |
| keynor-core | Backend — central API | Java + Spring Boot | In Development |
| summon-unity | Card game client | Unity 6 (C#) | In Development (rebuilt from scratch 2026-07-31) |
| keynor-rpg | RPG system | Java + Spring Boot | Planned — not scaffolded |
| keynor-stories | Literary orchestrator | Python | Planned — not scaffolded |
| summon-server | Card game server — accounts/rankings/collections | Java or Python (TBD) | Planned — not scaffolded |
| summon-game-engine | Card game server — authoritative match logic | Java + Spring Boot | Planned — not scaffolded |

Only the first three are active subprojects in this workspace today, with their own repositories and agent rosters. The other four are documented here as forward intent, not as folders to create speculatively — scaffold each only when real work on it begins.

`summon-unity-legacy` is **not** a subproject: it is the pre-rewrite Unity codebase (Unity 2021.3.16f1), kept as a plain, non-git reference folder at `../keynor-workspace/summon-unity-legacy/` purely so nothing from the earlier attempt is lost. No agent operates there, and it has no `.claude/` of its own.

---

## aniannoth-overview

Web interface for exploring the universe. Works as an interactive atlas — consumes the keynor-core REST API as its exclusive data source (Phase 2 — Phase 1's static JSON files have been fully removed).

### Stack
| Concern | Technology |
|---------|------------|
| Framework | React 19 + TypeScript |
| Build tool | Vite 8 |
| Routing | React Router v7 |
| Styling | Tailwind CSS v4, shadcn/ui (new-york style) |
| Map rendering | Leaflet.js (react-leaflet) |
| Markdown rendering | react-markdown |
| Data source | keynor-core REST API (`/api/public/v1/`) |

### Responsibilities
- Display and navigate universe content (characters, places, events, items, lore)
- Render interactive timeline with eras, and a navigable map (Leaflet.js) with location pins
- Filter content by era, map, and category simultaneously

See `aniannoth-overview/.claude/CLAUDE.md` for full component/state detail.

---

## keynor-core

Central API that persists and orchestrates all universe data. The authoritative source of truth for all universe entities — aniannoth-overview and every future service consume this API, never each other's data directly.

### Responsibilities
- CRUD for all universe entities
- Serve data to all other services
- Central authentication and authorization (OAuth2 Authorization Server + Resource Server)

### Architecture
- Hexagonal architecture (ports & adapters), domain layer with zero framework dependencies
- Java 21 + Spring Boot 3.3.4, PostgreSQL (Flyway migrations)

See `keynor-core/.claude/CLAUDE.md` for the full domain model, API surface, and security model.

---

## summon-unity

Client for the Summon card game. **It is a visual demonstration only** — see Business rules below for the non-negotiable security boundary this implies. Rebuilt from scratch 2026-07-31 on Unity 6 with UI Toolkit; the original 2021.3.16f1 codebase is preserved for reference only, in `summon-unity-legacy` (outside this governance system — see Subprojects above).

### Responsibilities
- Render account/collection/ranking/navigation screens sourced from `summon-server` (planned)
- Render match state and capture player intents once `summon-game-engine` (planned) exists
- Never compute a gameplay-numeric outcome locally and treat it as final

See `summon-unity/.claude/CLAUDE.md` for stack detail and current build state.

---

## Business rules and domain

### Universe entities (keynor-core)

All universe entities (`Character`, `Place`, `Faction`, `Item`, `Event`, `Lore`) share a common shape: `id`, `name`, `categories` (an entity may hold more than one), `tags`, `summary`, `body` (Markdown), `status`, `timeline` (`founded`/`destroyed`, nullable era strings), `createdAt`, `updatedAt`. `Place` additionally has `mapType` (`NAVIGABLE` or `ABSTRACT`).

**Entity status** is one of `CANON`, `DRAFT`, `DEPRECATED`. Valid transitions: `DRAFT → CANON`, `DRAFT → DEPRECATED`, `CANON → DRAFT`, `CANON → DEPRECATED`, `DEPRECATED → DRAFT`. **`DEPRECATED → CANON` is forbidden** — never allow an agent or a script to perform it.

The public API (`/api/public/v1/`, consumed by aniannoth-overview) **only ever returns `CANON` entities** — `DRAFT` and `DEPRECATED` must never be exposed there.

**Deletion policy:** universe/lore entities support hard delete. User account data (`users` table) must never be permanently deleted.

The universe has multiple **eras**, some of which predate the creation of the material world (no navigable map exists for those — `Place.mapType = ABSTRACT`).

### Summon card game — client-authoritative-security boundary

This is a hard invariant, not a style preference: **`summon-unity` (and any future client) never computes a gameplay-numeric outcome — damage, movement, valor, energy, or any value defined by the game rules — and treats it as final.** The client renders what the server tells it and sends *intents* ("move this card forward," "attack with this card"), never *values* ("deal 10 damage"). A client-authoritative value is a value a player could tamper with. Once `summon-server` (accounts/rankings/collections) and `summon-game-engine` (authoritative match logic) exist, this boundary spans both of them plus `summon-unity` — see Architectural Principles below for how that's expected to be governed.

---

## Architectural Principles

### Why microservices with hexagonal architecture?
The scope does not require microservices for technical necessity. The choice is intentional, for study purposes — learning the patterns in a real project with low operational risk. Each service starts modular and well-structured; extraction and integration between services happens incrementally as real need arises.

### Multi-repo architect precedent (for when summon-server / summon-game-engine are built)
When those two repositories are scaffolded, they will share the client-authoritative-security boundary above with `summon-unity` — a genuine cross-cutting invariant spanning all three. At that point, follow `CLAUDE.md`'s Level 3 "Multi-repo exception": a single architect (successor to `unity-architect`) may hold authority across all three repositories instead of one architect each, provided the invariant is documented explicitly in that agent's file along with a hard rule never to bundle changes from more than one of the three repositories into a single commit or PR.

---

## Inter-service Communication

- REST: aniannoth-overview ↔ keynor-core (`/api/public/v1/`, unauthenticated, CANON-only)
- REST: any content-authoring agent ↔ keynor-core's internal API (`/api/v1/`, Bearer JWT, ADMIN/SYSTEM roles)
- Planned once built: REST for summon-unity ↔ summon-server (accounts/collections); WebSocket/STOMP for summon-unity ↔ summon-game-engine (live match state)

---

## Testing strategy

This project does **not** author or maintain unit tests, in any subproject. All test coverage is integration and/or UI (Playwright) testing, executed by a dedicated `<stem>-tester` agent per active subproject, built from a shared external framework (`raqa-tester`) cloned into its own sibling repository. The tester agent writes its cycles from specification only, without reading the implementation it's testing, and is also responsible for running the suite and judging the result. See each subproject's own `.claude/CLAUDE.md` and its tester agent's file for specifics; see root `CLAUDE.md` for the policy itself.

---

## DevOps

To be implemented incrementally, per subproject, as each reaches that phase:
- Docker for containerization (keynor-core already ships a `Dockerfile`/`docker-compose.yml`)
- CI/CD via GitHub Actions — not yet configured for any subproject
- No unit tests anywhere in this project — see Testing strategy above

---

## Development Order

| Phase | Subprojects | Goal | Status |
|-------|-------------|------|--------|
| 1 | aniannoth-overview + static JSON | Visual foundation, navigable universe | Complete |
| 2 | keynor-core + aniannoth-overview | Central API, full migration from JSON to DB | In Progress |
| 3 | summon-unity | Client rebuild (Unity 6, UI Toolkit) ahead of any backend | In Progress |
| 4 | summon-server + summon-game-engine | Card game backends (accounts/rankings vs. authoritative match logic) | Planned |
| 5 | keynor-rpg | Game system built on the universe | Planned |
| 6 | keynor-stories | Literary orchestration in Python | Planned |

---

- 2026-09-22 — Overseer: migrated from `keynor-workspace`'s own governance system into this project. Subprojects, business rules, and architectural principles carried over and reconciled with the current (more evolved) `ai-wrapper` rule set — see root `CLAUDE.md`'s changelog for the governance-level changes this migration introduced.

*Last updated: 2026-09-22.*
