# Skill 21 — Context-Free Shared Artifacts

**Scope:** Any content an agent produces that will be read by someone outside the current conversation — a PR description or PR comment, a backlog/board card (Skill 14), any other human-facing shared document — and any comment or docstring written into source code, in any subproject.

### The problem this solves

During a task, an agent and the user talk in shorthand — an internal working name for a rule, a step, a layer, a decision ("regra 1", "camada 0", "the thing we discussed") — that only makes sense inside that specific conversation. That shorthand is useful for keeping the dialogue efficient, but it has no meaning once the session that produced it is gone. If it leaks into a PR, a board card, or a code comment, it leaves behind an artifact that only the people who were present for the original conversation can actually understand — everyone else, including the user's own future self, inherits confusion instead of information.

### Rule — human-facing documents

Any document meant to be read by someone outside the current session — a PR description or comment, a backlog/board card update (see Skill 14 for who may write one and when), any other write-up of what was delivered — must be **self-contained**: it explains, in its own words, what was built, changed, or decided, without relying on contextual shorthand coined during the conversation that produced it.

- Don't write "implements regra 1" or "per camada 0 as discussed" — state what the rule or layer actually is, and what changed because of it, so a reader with zero prior context understands the delivery.
- Brevity doesn't excuse ambiguity. A short, self-contained sentence is always preferable to a short, contextual one — summarizing is fine, coding the summary in session-only jargon is not.
- This rule governs *what* these artifacts must contain once written; it does not change *who* may write them or *when* — Skill 14's read-open/write-gated policy for the backlog, and Skill 01's PR flow, are unaffected.

### Rule — code comments

An agent never writes inline or block comments in source code, under any circumstance. Code stays comment-free; narrative about why a change was made, what task it belongs to, or what business rule it implements belongs in the task's own document (a spec, a PR description) — never inline in the file.

The single exception is doc-comment documentation on classes and methods/functions (Javadoc, or the equivalent convention for the language in use). Even there:

- Keep it concise and direct — a doc-comment is not a place to narrate the task's history.
- It must follow the same self-containment rule above: describe what the method or class does in terms any reader of the code can verify, never by referencing a conversation, a ticket shorthand, or a contextual label with no meaning outside the session that produced it.

This applies to comments the agent itself writes. It does not, by itself, authorize removing or rewriting pre-existing comments outside the task's actual scope (see Skill 16) — if existing in-code comments look like a project-wide problem worth fixing, report it rather than cleaning it up as a side effect.

### Rationale

Context is cheap to produce and expensive to preserve — conversational shorthand exists to make one conversation efficient, not to be read again later. Keeping it out of permanent, shared artifacts is what lets those artifacts outlive the session that created them: a PR, a board card, or a doc-comment should stand on its own long after the conversation that produced it is gone.
