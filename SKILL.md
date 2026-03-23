---
name: session-writeback
description: "Compact writeback policy for OpenClaw memory files. Use when deciding whether to record something from the current session, and where to store it: daily note, topic file, both, or nowhere."
---

# Session Writeback

Write only high-signal memory. Default is **do not write** unless the session produced something worth retrieving later.

## Storage Targets

- **Daily note:** `memory/YYYY-MM-DD.md`
  - Append-only log for session events, outcomes, decisions, blockers, and short facts tied to a day.
  - **Never rewrite, dedupe in place, or reorganize old entries.** Only append.
- **Topic file:** `memory/*.md`
  - Durable subject memory: projects, infrastructure, habits, preferences, recurring procedures, verified facts.
  - Update when the fact should survive beyond the day and be searchable by topic.
- **`MEMORY.md`:** rare. Only for high-level orientation or durable global constraints. Prefer topic files instead.

## Memory Hygiene

Prefer updating, merging, or summarizing existing durable memory over creating parallel notes. Avoid narrow duplicate topic files when an existing file already owns the subject.

## Classification Logic

### Write to daily note only
Use `memory/YYYY-MM-DD.md` when the information is time-bound, session-bound, or mainly useful as a dated trail.

Examples:
- what was worked on today
- a bug investigated, command tried, or temporary blocker
- a meeting/result/status change for today
- a short decision note with date context
- reminder-like facts that may later be distilled

### Write to topic file only
Use `memory/<topic>.md` when the information is durable and date is not the main retrieval key.

Examples:
- stable project architecture or API facts
- environment setup details
- persistent user preferences or constraints
- recurring workflow instructions
- lessons learned that should be found by subject later

### Write to both
Write **both** when the session created a durable fact **and** the dated event matters.

Pattern:
1. Append a short dated entry to today's daily note.
2. Add/update the durable distilled fact in the relevant topic file.

Examples:
- today a service moved to a new port, and that port should be remembered permanently
- today the user changed a standing preference
- today a project decision was made that affects future work

### Do not write
Skip writeback when the content is low-value, obvious, recoverable from files, or transient chatter.

Do **not** write:
- generic conversation filler
- facts already present in source files/code unless the new takeaway matters
- one-off tool output with no lasting value
- speculative ideas not adopted
- trivial completions ("opened file", "ran command")

## Heuristics

Write if at least one is true:
- user said **"remember this"**
- future retrieval would save time or prevent repeated mistakes
- it changes constraints, preferences, infra, process, or project state
- it records a meaningful decision, correction, or lesson

Otherwise, do not write.

## Mandatory Trigger Points

You must perform a writeback check immediately upon reaching a task boundary. Do not wait for the conversation to end. **If the check classifies anything as daily, topic, or both, execute the write immediately before proceeding to the next task or returning results.** A classification of "none" is valid only when the heuristics genuinely indicate no durable value. A task boundary is defined by any of the following events:

1. **Reusable Artifact Created:** You generated a new script, template, prompt, configuration, helper document, or other reusable file (for example in `tmp/` or `scripts/`).
2. **Important Decision or Recalibration:** Strategy, scope, workflow rules, filters, or target parameters were updated, narrowed, or meaningfully reinterpreted.
3. **Project State Changed:** You changed project state, regenerated or updated a meaningful output, fixed a configuration, verified a previously uncertain outcome, or completed a meaningful execution step whose result should be recoverable later.
4. **Durable User Input:** Jan stated a new preference, constraint, plan, concern, or other durable context that should affect future execution.

### Execution Rule

Write back incrementally during long or multi-topic sessions. If you are uncertain whether something warrants canonical memory, default to appending a short, concrete bullet point to the daily note.

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
- Keep entries compact and factual.
- Prefer timestamped bullets when useful.

Suggested format:

```md
- HH:MM brief fact / decision / result
```

or, if no time is needed:

```md
- Brief fact / decision / result
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

1. Classify: daily / topic / both / none.
2. If daily is needed, append a compact bullet to `memory/YYYY-MM-DD.md`.
3. If topic is needed, update or create the best matching `memory/*.md` file.
4. Avoid duplicating large text across files; daily note gets the event, topic file gets the distilled durable fact.
5. Do not perform cleanup or retroactive reorganization as part of normal writeback.

## Maintenance Rule

When a memory file grows noisy or repetitive, merge similar lessons, summarize verbose repeated entries, preserve confirmed preferences and boundaries, and do not delete durable memory casually.

## Output Expectation

When asked to perform writeback, return briefly:
- what was written
- exact file path(s)
- classification used: daily / topic / both / none
