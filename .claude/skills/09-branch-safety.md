# Skill 09 — Branch Safety Check

**Scope:** Any agent about to push a commit to a branch that already has an open pull request, in any repository.

### The rule

Once a PR has been submitted for human review, treat that branch as **potentially already merged** — even if only minutes have passed. Before pushing an additional commit to it, verify its current PR state (e.g. via your Git host's CLI or API) rather than assuming the branch is still open and yours to extend.

If the PR is already merged: switch to `main`, pull, and open a fresh `task/*` branch for the new work. Never push to a merged branch — the commit will effectively be orphaned from the history that matters, and can silently reintroduce already-superseded code.

This applies equally when resuming work in a new session on a branch you (or another agent) opened earlier — always check PR state before the first new commit of that session, not just the first time the branch was ever pushed to.

### Rationale

An agent has no way to know, just by looking at its own local branch state, whether a human has acted on the PR in the meantime. The check is cheap; assuming the branch is still "yours" and being wrong about it is not — it can mean lost work, a confusing merge conflict, or a second PR racing the first one.
