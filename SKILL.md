---
name: session-writeback
description: "Compact writeback policy for OpenClaw memory files. Use when deciding whether to record something from the current session, and where to store it: daily note, topic file, both, or nowhere."
---

# Session Writeback

Write only high-signal memory. Default is **do not write** unless the session produced something worth retrieving later.

## Canonical Ownership

**Topic files are the canonical durable store.** Any information that should be retrievable by subject — not by date — belongs in a topic file. Daily notes are a minimal session ledger, not a content store. When in doubt between topic file and daily note, the answer is topic file. Daily note is only for dated event traces.

## Storage Targets

- **Topic file:** `memory/*.md` — **primary durable storage**
  - Durable subject memory: projects, infrastructure, habits, preferences, recurring procedures, verified facts, reusable lessons, personal context.
  - Update when the fact should survive beyond the day and be searchable by topic.
- **Daily note:** `memory/YYYY-MM-DD.md` — **session ledger only**
  - Minimal append-only log: what sessions ran, what tasks completed, what was delivered.
  - **Not a content store.** Do not write durable facts, preferences, lessons, or context here unless they are purely time-bound events.
  - **Never rewrite, dedupe in place, or reorganize old entries.** Only append.
- **`MEMORY.md`:** rare. Only for high-level orientation or durable global constraints. Prefer topic files instead.

## Memory Hygiene

Prefer updating, merging, or summarizing existing durable memory over creating parallel notes. Avoid narrow duplicate topic files when an existing file already owns the subject.

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

### 2. Write to topic file only (default for durable content)
Use `memory/<topic>.md` when the information is durable and should be retrievable by subject.

This is the **default target for anything worth remembering.** If it passes the "do not write" filter, it almost certainly belongs here.

Examples:
- stable project architecture or API facts
- environment setup details
- persistent user preferences or constraints
- recurring workflow instructions
- lessons learned that should be found by subject later
- personal context, goals, psychology notes
- infrastructure state, service configs, tool decisions

### 3. Write to both (rare — requires explicit justification)
Write **both** only when a durable fact was created **and** the specific date of the event materially matters for future retrieval.

Pattern:
1. Add/update the durable distilled fact in the relevant topic file (primary).
2. Append a one-line dated reference to today's daily note (secondary).

Examples:
- today a service moved to a new port — port in topic file, migration event in daily note
- today the user changed a standing preference — preference in topic file, change event in daily note

### 4. Write to daily note only (session ledger entries)
Use `memory/YYYY-MM-DD.md` **only** for information that is purely time-bound and has no durable subject-retrieval value.

Examples:
- what sessions ran and what tasks were completed today
- a temporary blocker that was resolved in the same session
- a session checkpoint for continuity recovery

**Anti-pattern:** If you find yourself writing a durable fact, preference, lesson, decision, or piece of personal context to the daily note because you are unsure where it goes — stop. Find or create the right topic file instead. The daily note is not an uncertainty sink.

## Heuristics

Write if at least one is true:
- user said **"remember this"**
- future retrieval would save time or prevent repeated mistakes
- it changes constraints, preferences, infra, process, or project state
- it records a meaningful decision, correction, or lesson
- the session produced a durable artifact, configuration change, or state change

**Mandatory write after durable output:** If a Mandatory Trigger Point fired (artifact created, decision made, state changed, durable user input), a classification of "none" requires explicit justification — the content must genuinely fail all heuristic tests. The trigger point firing creates a strong presumption that something should be written.

Otherwise, do not write.

## Mandatory Trigger Points

You must perform a writeback check immediately upon reaching a task boundary. Do not wait for the conversation to end. **If the check classifies anything as daily, topic, or both, execute the write immediately before proceeding to the next task or returning results.** A classification of "none" is valid only when the heuristics genuinely indicate no durable value. A task boundary is defined by any of the following events:

1. **Reusable Artifact Created:** You generated a new script, template, prompt, configuration, helper document, or other reusable file (for example in `tmp/` or `scripts/`).
2. **Important Decision or Recalibration:** Strategy, scope, workflow rules, filters, or target parameters were updated, narrowed, or meaningfully reinterpreted.
3. **Project State Changed:** You changed project state, regenerated or updated a meaningful output, fixed a configuration, verified a previously uncertain outcome, or completed a meaningful execution step whose result should be recoverable later.
4. **Durable User Input:** the user stated a new preference, constraint, plan, concern, or other durable context that should affect future execution.

### Execution Rule

Write back incrementally during long or multi-topic sessions. Do not batch writes to session end.

**Uncertainty resolution:** If you are uncertain whether something warrants a write, apply the heuristics. If the heuristics say write, route it to the correct target per Classification Logic. If you are uncertain about *which* topic file, find the best match or create one — do not route to the daily note as a fallback. The daily note is never a classification escape hatch.

### Subagent Writeback Rule

Subagents must apply the same Mandatory Trigger Points as the main session. Before producing your final response:
1. Review artifacts created, decisions made, and state changes during execution.
2. Classify each per the standard logic (daily / topic / both / none).
3. Execute any resulting writes before your final message.
4. Report what you wrote (file paths and classification) in your final message so the parent session can verify.

The subagent that produced the artifact is the primary owner of the write. The parent session's Session-End Writeback Gate (AGENTS.md) serves as a fallback — if a subagent did not persist durable results, the parent must catch and write them.

## Daily Note Rules

- File path: `memory/YYYY-MM-DD.md`
- If missing, create it.
- **Append only** at end of file.
- Keep entries compact and factual — one line per event.
- Prefer timestamped bullets when useful.
- **Content ceiling:** Each entry should be at most 1–2 lines. If you need more, the content belongs in a topic file, not here.
- **Self-check:** If a daily note exceeds ~30 lines in a day, review whether durable content is leaking in that should be in topic files.

Suggested format:

```md
- HH:MM brief event / task completed / artifact delivered
```

or, if no time is needed:

```md
- Brief event / task completed / artifact delivered
```

## Topic File Rules

- Prefer an existing relevant file over creating a near-duplicate.
- Keep topic files organized by subject, not by date.
- Distill; do not dump raw transcripts.
- Preserve durable truths, not session noise.

Suggested sections:
- Facts
- Decisions
- Preferences
- Procedures
- Open issues

## `MEMORY.md` Rule

Use only when the fact belongs in always-loaded baseline memory, such as:
- enduring environment facts
- global operating constraints
- permanent orientation notes

If unsure, use a topic file instead.

## Writeback Procedure

1. Classify per Classification Logic: none / topic / both / daily.
2. If topic is needed, update or create the best matching `memory/*.md` file.
3. If daily is needed, append a compact bullet to `memory/YYYY-MM-DD.md`.
4. Avoid duplicating large text across files; daily note gets the event, topic file gets the distilled durable fact.
5. Do not perform cleanup or retroactive reorganization as part of normal writeback.

## Maintenance Rule

When a memory file grows noisy or repetitive, merge similar lessons, summarize verbose repeated entries, preserve confirmed preferences and boundaries, and do not delete durable memory casually.

## Output Expectation

When asked to perform writeback, return briefly:
- what was written
- exact file path(s)
- classification used: daily / topic / both / none
