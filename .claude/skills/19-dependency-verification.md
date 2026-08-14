# Skill 19 — Dependency Verification

**Scope:** Any task that would add, change, or recommend a third-party package or library, in any subproject.

### The rule

Never name a package or a version in a proposed change without having just confirmed, in the current session, that it actually exists — via the package registry, the project's lockfile, an installed-packages listing, or an explicit search. A plausible-sounding package name or a plausible-sounding version number is not a substitute for a checked one.

This applies before the change even reaches the authorization step: adding, removing, or upgrading a dependency is already a protected action under the root `CLAUDE.md` requiring explicit user authorization — this skill closes the gap *before* that gate, so the proposal the user is asked to authorize was never fabricated in the first place.

### What this looks like in practice

- Before writing `some-library: "^3.2.0"` into a manifest, confirm `3.2.0` (or whatever version is being proposed) is a real published version, not a guess at what the next version "probably" is.
- Before recommending a package by name, confirm it exists under that exact name on the registry this project actually uses — a similarly-named but different package is a common and costly mix-up.

### Rationale

A fabricated package name or version, if it slips through, fails at install time in the best case — and in the worst case, matches the name of an actual (potentially malicious) package that does exist, which is exactly the mechanism behind dependency-confusion and typosquatting attacks. Verifying before proposing removes that risk at its source rather than relying on the human authorizer to catch it.
