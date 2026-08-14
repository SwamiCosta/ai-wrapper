# Skill 20 — Completion Evidence

**Scope:** Any agent about to report a task, or part of a task, as done.

### The rule

A claim of completion is only as good as the evidence behind it. "Tests pass," "verified in the browser," "the build succeeds" are conclusions — state the actual command that was run and its actual output, or the actual steps taken to check a UI, alongside the conclusion. An assertion of success with no cited evidence is treated as **unverified**, not as done, even if it's probably true.

This is not about padding every report with logs — it's about the difference between "I ran `npm test` and all 42 tests passed" and "tests pass." The first is checkable; the second is a claim the reader has to either trust blindly or re-verify themselves, which defeats the purpose of the agent having checked at all.

### What this looks like in practice

- Reporting a bug fix: cite the specific test that now passes (and failed before the fix), not just "fixed."
- Reporting a UI change: state that the dev server was started, the feature was exercised in a browser, and what was observed — or explicitly say this wasn't done and why, rather than implying it was.
- Reporting a migration ran cleanly: cite the actual migration tool's output, not "ran the migration."

### Rationale

Agents are prone to describing a task as complete based on how the change *looks* rather than on having actually executed and observed the result — the code compiles, the logic reads correctly, so it "should" work. Requiring the actual evidence, not just the conclusion, is what closes the gap between "looks complete" and "confirmed complete."
