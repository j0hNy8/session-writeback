---
name: session-writeback
description: "Compact writeback policy for OpenClaw memory files. Use when deciding whether to record something from the current session, and where to store it: MEMORY.md, today's daily note, a derived/reference topic file, or nowhere."
---

# Session Writeback

Write only high-signal memory. Default is **do not write** unless the session produced something worth retrieving later.

## Canonical Ownership

**`MEMORY.md` is the live long-term durable store.** Stable facts, preferences, workflow rules, infrastructure state, recurring lessons, and enduring project context belong there unless there is a strong reason to maintain a separate derived/reference topic file. **Daily notes are the curated episodic layer.** Use them for concise chronology, rationale, session context, and checkpoints that help future recall. **Topic files are derived/reference artifacts, not routine write targets.** They may be created or refreshed by higher-level maintenance flows such as dream-cycle extraction, but normal skills and maintenance logic should not treat them as the default canonical destination.

## Storage Targets

- **`MEMORY.md`** — **primary long-term memory**
  - Durable user context, preferences, rules, infrastructure facts, ongoing-project truth that should survive beyond the day, and other cross-session memory worth loading/searching later.
  - Prefer updating an existing section over scattering the same fact into separate topic files.
- **Daily note:** `memory/YYYY-MM-DD.md` — **curated daily ledger**
  - Minimal append-only log plus concise chronology, rationale, session context, and useful retrieval cues tied to the day.
  - **Not a transcript store.** Do not dump raw dialogue or large durable content here; distill to the smallest useful episodic note.
  - **Never rewrite, dedupe in place, or reorganize old entries.** Only append, except when the user explicitly requests cleanup of the current day.
- **Derived/reference topic file:** `memory/*.md` when explicitly justified
  - Use only when a subject-specific extracted/reference artifact is materially useful and the information is being organized out of `MEMORY.md`, not because routine writeback needs a canonical topic owner.
  - Leave a clear relationship to `MEMORY.md`; do not create split-truth canonical ownership.

## Memory Hygiene

Prefer updating or consolidating existing sections in `MEMORY.md` over creating parallel topic files. Avoid duplicate subject files unless a derived/reference split is intentional and maintained by a higher-level flow.

## Classification Logic

Classify in this order. Stop at the first match.

### 1. Do not write
Skip writeback when the content is low-value, obvious, recoverable from files, or transient chatter.

Do **not** write:
- generic conversation filler
- facts already present in source files/code unless the new takeaway matters
- one-off tool output with no lasting value
- speculative ideas not adopted
- trivial completions ("opened file", "ran command")

### 2. Write to `MEMORY.md` (default for durable content)
Use `MEMORY.md` when the information is durable and should be retrievable across sessions as current long-term memory.

This is the **default target for anything worth remembering long-term.** If it passes the "do not write" filter and is not just a same-day ledger event, it usually belongs here.

Examples:
- stable project architecture or API facts
- environment setup details
- persistent user preferences or constraints
- recurring workflow instructions
- lessons learned that should be found later
- personal context, goals, psychology notes
- infrastructure state, service configs, tool decisions

### 3. Write to both (`MEMORY.md` + daily note)
Write **both** when the session produced a durable fact **and** concise dated context would improve future recall.

Pattern:
1. Add/update the durable distilled fact in `MEMORY.md` (primary).
2. Append a concise daily note entry capturing the chronology, rationale, session context, or retrieval phrasing that helps explain the change (secondary).

Examples:
- today a service moved to a new port — port in `MEMORY.md`, migration context in daily note
- today the user changed a standing preference — preference in `MEMORY.md`, reason/change context in daily note
- today a workflow rule was recalibrated — rule in `MEMORY.md`, trigger context in daily note

### 4. Write to daily note only (episodic or time-bound entries)
Use `memory/YYYY-MM-DD.md` when the information is useful mainly as concise episodic recall and does not warrant a durable `MEMORY.md` update.

Examples:
- what sessions ran and what tasks were completed today
- a temporary blocker that was resolved in the same session
- a short rationale note or continuity checkpoint likely to help future recovery
- a useful paraphrased retrieval cue tied to today's work, with no durable long-term fact behind it

**Anti-pattern:** If you find yourself writing a durable fact, preference, lesson, decision, or piece of personal context to the daily note because you are unsure where it goes — stop. Put it in `MEMORY.md` or skip it. The daily note is not an uncertainty sink.

## Heuristics

Write if at least one is true:
- user said **"remember this"**
- future retrieval would save time or prevent repeated mistakes
- it changes constraints, preferences, infra, process, or project state
- it records a meaningful decision, correction, or lesson
- the session produced a durable artifact, configuration change, or state change

**Mandatory write after durable output:** If a Mandatory Trigger Point fired (artifact created, decision made, state changed, durable user input), a classification of "none" requires explicit justification — the content must genuinely fail all heuristic tests. The trigger point firing creates a strong presumption that something should be written.

Otherwise, do not write.

## Write Resolution (ADD / UPDATE / DELETE / NOOP)

Before committing any write, classify the operation. This prevents duplicate accumulation, stale facts, and noise.

| Operation | When to use |
|---|---|
| **ADD** | Genuinely new durable fact not present anywhere in memory. Create or extend the relevant section in `MEMORY.md` (or a justified derived/reference file if that structure already exists). |
| **UPDATE** | An existing fact has changed. Locate the existing entry in `MEMORY.md` or the justified derived/reference file and edit it in-place. Do **not** append a second copy — find and replace the old value. |
| **DELETE** | A fact is no longer valid. Remove the entry or mark it `[SUPERSEDED — <brief reason>]` if historical context has value. |
| **NOOP** | Content is transient, already correctly stored, not durable, or fails the "do not write" filter. Skip entirely. |

### Resolution Rules

1. Before writing, run `memory_search` (or read the target file) to check whether the fact already exists.
2. If it exists and is **unchanged** → **NOOP**.
3. If it exists and has **changed** → **UPDATE** in-place in the canonical file.
4. If it does **not exist** → **ADD**.
5. If it exists but is **no longer valid** → **DELETE** or mark `[SUPERSEDED]`.
6. **Never append a duplicate entry.** If a fact already appears in `MEMORY.md` or an intentional derived/reference file, UPDATE it there — do not add a second copy elsewhere.
7. Report the resolution used (ADD / UPDATE / DELETE / NOOP) in the writeback output alongside the file path.

## Mandatory Trigger Points

You must perform a writeback check immediately upon reaching a task boundary. Do not wait for the conversation to end. **If the check classifies anything as daily, `MEMORY.md`, or both, execute the write immediately before proceeding to the next task or returning results.** A classification of "none" is valid only when the heuristics genuinely indicate no durable value. A task boundary is defined by any of the following events:

1. **Reusable Artifact Created:** You generated a new script, template, prompt, configuration, helper document, or other reusable file (for example in `tmp/` or `scripts/`).
2. **Important Decision or Recalibration:** Strategy, scope, workflow rules, filters, or target parameters were updated, narrowed, or meaningfully reinterpreted.
3. **Project State Changed:** You changed project state, regenerated or updated a meaningful output, fixed a configuration, verified a previously uncertain outcome, or completed a meaningful execution step whose result should be recoverable later.
4. **Durable User Input:** the user stated a new preference, constraint, plan, concern, or other durable context that should affect future execution.

### Execution Rule

Write back incrementally during long or multi-topic sessions. Do not batch writes to session end.

**Uncertainty resolution:** If you are uncertain whether something warrants a write, apply the heuristics. If the heuristics say write, route it to the correct target per Classification Logic. If you are uncertain how to place it in `MEMORY.md`, update the closest existing section there instead of creating a new topic file by reflex. Do not route to the daily note as a fallback. The daily note is never a classification escape hatch.

### Subagent Writeback Rule

Subagents must apply the same Mandatory Trigger Points as the main session. Before producing your final response:
1. Review artifacts created, decisions made, and state changes during execution.
2. Classify each per the standard logic (`MEMORY.md` / daily / both / none).
3. Execute any resulting writes before your final message.
4. Report what you wrote (file paths and classification) in your final message so the parent session can verify.

The subagent that produced the artifact is the primary owner of the write. The parent session's Session-End Writeback Gate (AGENTS.md) serves as a fallback — if a subagent did not persist durable results, the parent must catch and write them.

## Daily Note Rules

- File path: `memory/YYYY-MM-DD.md`
- If missing, create it.
- **Append only** at end of file.
- Keep entries compact and factual — usually one line per event, occasionally two when brief rationale matters.
- Prefer timestamped bullets when useful.
- **Content ceiling:** Each entry should be at most 1–2 lines. Capture the smallest useful episodic context; if you need more, the content belongs in `MEMORY.md`, not here.
- **Self-check:** If a daily note exceeds ~30 lines in a day, review whether durable content is leaking in that should be in `MEMORY.md` or whether entries have become too transcript-like.

Suggested format:

```md
- HH:MM brief event / task completed / artifact delivered
```

or, if no time is needed:

```md
- Brief event / task completed / artifact delivered
```

## Derived/Reference Topic File Rules

- Prefer `MEMORY.md` unless a subject-specific derived/reference file already exists for a real reason.
- Do not create a near-duplicate just to satisfy routine writeback.
- Keep derived/reference files organized by subject, not by date.
- Distill; do not dump raw transcripts.
- Preserve durable truths, not session noise.

Suggested sections:
- Facts
- Decisions
- Preferences
- Procedures
- Open issues

## `MEMORY.md` Rule

Use `MEMORY.md` for normal long-term memory, such as:
- enduring environment facts
- global operating constraints
- durable user context and preferences
- live project truth worth carrying across sessions
- permanent orientation notes

If unsure, use `MEMORY.md` instead of creating a topic file.

## Writeback Procedure

1. Classify per Classification Logic: none / `MEMORY.md` / both / daily.
2. If durable memory is needed, update the right section in `MEMORY.md` (or a justified derived/reference file if one already owns the extracted view).
3. If daily is needed, append a compact bullet to `memory/YYYY-MM-DD.md`.
4. Avoid duplicating large text across files; `MEMORY.md` gets the distilled durable fact, while daily notes get only the concise episodic event/context that improves recall.
5. Do not perform cleanup or retroactive reorganization as part of normal writeback.

## Maintenance Rule

When a memory file grows noisy or repetitive, merge similar lessons, summarize verbose repeated entries, preserve confirmed preferences and boundaries, and do not delete durable memory casually.

## Output Expectation

When asked to perform writeback, return briefly:
- what was written
- exact file path(s)
- classification used: `MEMORY.md` / daily / both / none
