# Skill 03 — Test Coverage

**Scope:** Any task that writes or modifies source code, including test code, in any subproject.

### Role separation

The agent that authors a feature and the agent (or role) that authors its tests are treated as separate by default — not because one agent can't write correct tests for its own code, but because a test written by the same hand that wrote the implementation tends to encode the implementation's own blind spots rather than catch them. If your project only has one developer agent per subproject, that agent still writes the tests, but treat "does this test actually exercise the rule, or just the happy path the code already handles" as a question for the architect's review (Skill 04), not something the author self-certifies.

### Baseline coverage, not exhaustive coverage

Default to: the happy path, plus one pair of cases that exercises a real business rule both satisfied and violated (e.g. a validation rule: one case that passes it, one that fails it and asserts the correct rejection). This is a floor, not a ceiling — add more when a rule is genuinely complex or has caused a real bug before, but open-ended edge-case sprawl by default costs more in review time than it returns in caught bugs.

### What "done" means for a coverage requirement

New code ships with tests in the same PR, not as a follow-up. A PR that adds behavior without a corresponding test is incomplete, not merely lower-quality.

### Rationale

Untested code is a claim, not a fact. The baseline above is calibrated to catch the failure modes that actually recur — an unhandled edge of a business rule, a path nobody exercised — without turning every small change into an exhaustive-test-matrix exercise that mostly just re-describes the implementation in a different syntax.
