# Skill 17 — Test Integrity

**Scope:** Any task where a test is failing, flaky, or otherwise not behaving as expected, in any subproject.

### A red test is signal, not an obstacle

A failing test describes a real disagreement between what the code does and what was expected. An agent may never edit a test's assertions, skip it, delete it, or loosen its tolerance purely to turn a build green. Changing what a test asserts is only ever done when the old assertion was actually wrong — and that determination, along with the reason, is stated explicitly in the PR, not silently folded into an unrelated change. For anything beyond a clearly-in-scope fix, this is exactly the kind of call Skill 04 (architect review) exists to catch, not something a developer agent self-certifies.

### Flakiness requires root cause, never a silent patch

An intermittently failing test is never "fixed" with an arbitrary `sleep`, a blind retry wrapper, or a widened tolerance without first identifying why it's flaky. Either:

- the actual race condition, timing dependency, or shared-state issue is found and fixed, or
- the test is explicitly flagged as known-flaky, with the reason recorded, so it's a tracked problem rather than a silently-hidden one.

A retry loop that makes a flaky test "pass reliably" without addressing the underlying cause doesn't fix the bug — it just makes the bug harder to notice.

### Rationale

Both of these are well-documented failure modes in AI-assisted development specifically: an agent under pressure to show a green build has every incentive to make the signal go away rather than fix what it's signaling. Treating the test itself as something that can only change for a stated, reviewed reason keeps that pressure from quietly eroding what the test suite actually verifies.
