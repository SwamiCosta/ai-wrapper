# Skill 10 — Investigation Hygiene

**Scope:** Any agent performing a read-only investigation (reconstructing an incident, comparing versions, tracing history) before producing a report or a plan, in any subproject.

### When this skill applies

Whenever answering a question requires gathering evidence from more than one location — reconstructing a sequence of events from Git history, comparing multiple file versions, searching across several files. It does not apply to a single, already-known lookup.

### Standard procedure

1. Before running an inspection command, state the specific question it's meant to answer.
2. Prefer the narrowest command that can answer that question over a broad dump — a scoped `git log -n <N> <ref>` instead of `git log --all --graph`, a path-scoped diff instead of an unscoped one, a filtered branch list instead of listing every branch.
3. If answering the question needs multiple rounds of broad search across many files or an unknown number of locations, and the runtime provides a delegation mechanism for isolated sub-investigations, delegate the raw search to it and bring back only the synthesized conclusion — not the raw output. If no such mechanism exists, this step doesn't apply.
4. Discard or summarize raw output as soon as its one-time analytical purpose is served. Don't leave large dumps sitting around for the rest of the task.

### What this doesn't require

No user authorization — this is a default execution practice, not a protected action. It doesn't replace Skill 08 or Skill 09; sync timing and branch safety are unaffected by how an investigation is conducted.

### Rationale

Long sessions degrade in quality and cost more as they fill with raw, low-density output that served a one-time purpose and adds no value once consumed. Asking the narrowest possible question, and delegating broad searches when the runtime supports it, keeps the working context focused on conclusions rather than the process used to reach them.
