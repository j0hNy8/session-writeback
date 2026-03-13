# session-writeback
Deterministic memory management for OpenClaw. Enforces strict writeback discipline at task boundaries to preserve high-signal context without memory bloat or parallel silos.


Session Writeback
A strict memory preservation skill that replaces continuous, noisy chat logging with discrete, high-signal writebacks. It ensures the agent only commits durable facts and session state to canonical files when actual work is completed.

Core Mechanics:

Mandatory Triggers: Executes strictly at defined task boundaries (artifact creation, state changes, key decisions), avoiding random writes during standard conversation.

Targeted Routing: Sorts transient events into append-only daily notes (YYYY-MM-DD.md) and durable rules into subject-specific topic files (*.md).

Memory Hygiene: Prevents file sprawl by forcing the agent to update, merge, or summarize existing records instead of creating duplicate files or blindly deleting history.
