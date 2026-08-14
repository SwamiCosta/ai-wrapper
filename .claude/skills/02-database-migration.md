# Skill 02 — Database Migration & Reversibility

**Scope:** Any task that changes a database schema or the data in it, in any subproject with a database.

### Migration vs. seed — different operations, different rules

A **migration** changes structure: new tables, new columns, constraints, indexes. A **seed** changes data within an existing structure. They are not interchangeable, and a task that blurs the two (a migration script that also inserts rows, a "seed" that silently alters a column type) should be split before it's authored, not after review catches it.

### Destructive operations require per-statement authorization

`DROP`, `TRUNCATE`, a column type change that can lose data, or any `DELETE`/`UPDATE` without a narrow `WHERE` — each one is a protected action under the root `CLAUDE.md` and needs its own explicit user authorization. Never bundle a destructive statement into a migration alongside non-destructive ones and get one blanket sign-off for the whole file; call out the destructive line specifically.

### Sequence non-destructive changes first

When a migration set includes both additive and destructive changes, apply and verify the additive changes first. This keeps a partial rollback simpler if something goes wrong midway.

### Reversibility

Every migration ships with a stated rollback path — a down-migration, a documented manual undo procedure, or an explicit note on why this specific change genuinely can't be reversed (e.g. an irreversible data transformation). "We'll figure out rollback if we need it" is not an acceptable answer at authoring time; write the rollback note in the same PR as the migration itself, not as a follow-up.

### Rationale

A schema change that goes wrong is one of the few classes of bug that can't always be fixed by reverting a commit — the data may already be in a shape the old code can't read. Separating structure from data, gating destructive statements individually, and requiring a rollback plan up front are all ways of making sure the irreversible part of the change was a deliberate choice, not a side effect nobody looked at closely.
