# Skill 22 — Lean Documentation Maintenance

**Scope:** Any edit to a document that accumulates over time and is read repeatedly by agents or humans — `CLAUDE.md` (root or subproject), `ARCHITECTURE.md`, `SKILLS.md`, any skill file, any hand-maintained reference doc (Skill 06) — in any repository.

### The problem this solves

Documents like `CLAUDE.md` and `ARCHITECTURE.md` are read in full, repeatedly, by every agent that touches the project (see `CLAUDE.md`'s "Mandatory reading order"). Every unnecessary sentence in them is a cost paid on every single read, not just once at write time. Left unchecked, these documents grow monotonically — a new decision gets appended next to the old one it supersedes, a topic gets re-explained instead of extended — until reading them in full becomes expensive enough that the document defeats its own purpose.

### Rule — size discipline

Before adding to a document, treat its length as a cost, not a free resource. Prefer the smallest edit that preserves all the information that must survive, over the easiest one to make. A change that can be made in place — updating a sentence, replacing a stale value in a table — should not instead be appended as a new paragraph next to the old one.

### Rule — edit and extend, don't duplicate or re-explain

When new information supersedes, extends, or refines something already written, revise that existing text directly. Add only the delta — what's actually new — rather than restating the topic from scratch alongside the old explanation. If a rule changes, replace the old statement; don't leave both the superseded version and the new one stacked in the same document.

- **Exception — genuine historical records.** A changelog footer, a "Last updated" log, or any other section that is explicitly a chronological record of past decisions is meant to be append-only by design (see the changelog entries already in this repo's `CLAUDE.md` footer). This rule governs a document's primary descriptive content, not its own intentional history log.
- This is Skill 16's (Scope Discipline) discipline applied to documentation instead of code: the result should read as one coherent document, not as a transcript of every time someone touched it.

### Rule — structure for partial reading

Any document expected to grow large — `CLAUDE.md`, `ARCHITECTURE.md`, any subproject `CLAUDE.md` that grows past a quick skim — must be organized into clearly labeled sections with a navigable index (a table of contents near the top, linking to each section) once scanning it end-to-end stops being the fastest way to find something. This is what makes `CLAUDE.md`'s own "Mandatory reading order" rule practical — a Level 1/2 agent told to read only their subproject's section of `ARCHITECTURE.md` needs an index to find and stop at that section, not a reason to read the whole document to locate it.

### Rationale

These documents earn their keep by being read completely and often. Conciseness and non-duplication keep that read cheap; a navigable structure keeps a targeted read actually possible instead of theoretical. A document that only ever grows, and never gets tightened, eventually costs more to read than it saves.
