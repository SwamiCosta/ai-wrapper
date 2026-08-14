# Skill 07 — Logging Conventions

**Scope:** Any task that adds or changes a log statement, in any subproject.

### Log levels

| Level | Use for |
|---|---|
| ERROR | Something failed and needs human attention — an unhandled exception, a failed external call with no fallback |
| WARN | Something unexpected happened but the system recovered or degraded gracefully |
| INFO | A significant, expected event worth a permanent record — a request handled, a job completed |
| DEBUG | Detail useful during active development, not expected to be on in production by default |

Pick the level by what a person monitoring the system in production would want to see, not by how the code feels to the person writing it in the moment.

### Correlation

If a request or task can span multiple log lines (a request handled across several layers, a background job with multiple steps), attach a correlation/trace ID at the entry point and propagate it through every log line for that unit of work — through your stack's equivalent of a logging context (thread-local, async-local, a request-scoped object; the exact mechanism is stack-specific and belongs in a project-level skill, not here).

### What never gets logged

Passwords, tokens, full credit card numbers, or any other secret or credential — not even at DEBUG level, not even truncated in a way that still leaks entropy. If a value's presence in logs would itself be a security incident, it doesn't go in a log statement, full stop.

### Layer-based logging points

Log at the boundary of a layer (an inbound request arriving, an outbound call being made, a layer catching and translating an error) rather than scattering log lines through the middle of business logic. This keeps the log output readable as a trace of what the system did, not a trace of every line the code happened to execute.

### Rationale

Logs are read under pressure, usually by someone who wasn't the one who wrote the code. A consistent level scheme and a propagated correlation ID are what make it possible to reconstruct what happened without already knowing the answer.
