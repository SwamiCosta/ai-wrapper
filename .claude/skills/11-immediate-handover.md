# Skill 11 — Immediate Handover

**Scope:** Any agent file that describes one agent "signaling," "notifying," or "handing off" to another named agent as part of a documented workflow, in any subproject.

### The default

When an agent's documented workflow calls for signaling or handing off to another named agent, the calling agent must **stop and produce a handoff report**. It must not continue in the same session as the target agent, and must not invoke it as a sub-agent, by default — regardless of how low-risk the next step looks, or whether the calling agent already has every piece of context the target would need.

This default exists for cost, not safety. Chaining personas in the same session compounds token spend — each persona switch re-reads its own mandatory documents and grows the working context — and that cost should be a choice the user makes deliberately, not a standing default any agent file opts into on its own.

### What a handoff report contains

- What was completed, and by which agent
- Which named agent the handoff is to, and what they're expected to do
- Any context, data, or identifiers the target will need (a PR URL, file paths, a branch name, an identifier)
- Why the handoff is needed now

Put it wherever it's most discoverable: a comment on the open PR if there is one, or a direct message to the user if there isn't. Then stop — don't act further as the target agent.

### The exception — explicit user authorization

The user may authorize an immediate, same-session handoff for a specific instance ("go ahead and continue as X now"). Only with that authorization does the calling agent continue in the same session as the target, or invoke it as a sub-agent. Authorization is scoped to the instance it was given for — a standing "always continue immediately" instruction defeats the purpose of this skill; ask again the next time a handoff point is reached unless the user states the exception is durable.

### Skill 03 and Skill 04 — separation gates, not exceptions to ask

Skills 03 (author/test separation) and 04 (architect review) describe handoffs that exist specifically to keep authorship separate from testing or review. This skill's default — stop and report, rather than continue as the same pass — already satisfies that separation. Even if the user authorizes immediate continuation generally, authorizing it *for a Skill 03 or Skill 04 handoff specifically* collapses the separation those skills exist to provide (an agent writing its own tests, or reviewing its own PR). Surface that trade-off back to the user before proceeding rather than treating it as routine.

### Rationale

A documented handoff exists to make clear *who* is responsible for the next step. Defaulting to "stop and report," with immediate continuation as an explicit opt-in exception, puts the latency/cost trade-off in the user's hands on every occurrence rather than baking a single answer into the project.
