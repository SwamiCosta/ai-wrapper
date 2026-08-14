# Skill 18 — Secrets Hygiene

**Scope:** Any commit, in any repository, by any agent.

### The rule

Before staging a broad `git add`, review what's actually included (`git status` after the add, not before). If anything looks like it could plausibly hold a credential, API key, token, private key, or connection string — even with an innocuous-looking filename — check its actual contents before committing it, not just its name. `.env`, `.env.*`, and any file explicitly listed under the root `CLAUDE.md`'s Protected Actions → Configuration files are never committed at all.

If a secret is found already committed in history — not just staged — stop and report it rather than attempting to silently scrub it. Removing a secret from the latest commit doesn't remove it from history, and rewriting history is itself a protected, high-blast-radius action that needs explicit user authorization (see the root `CLAUDE.md` — Protected actions, Git).

### Why this is a project rule, not just a tool default

Secret-scanning behavior that only exists inside one particular coding tool's own safety net doesn't survive a change of tooling, a different agent, or a human running `git commit` directly. Writing the check down as a project rule means it applies regardless of which agent or tool is doing the committing.

### Rationale

A committed secret is one of the few mistakes in software development that isn't fully undone by "just revert it" — once it's pushed, especially to a shared or public remote, it should be treated as compromised and rotated, not merely deleted. Catching it before the commit is cheap; catching it after requires a credential rotation and, if the repo is public, may already be too late.
