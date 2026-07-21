---
name: session-handover
description: Use when the user asks to save session context, a handover doc, or memory so a fresh session can continue ("save this session memory/handover doc", "before the usage limit hits"), or when starting a session with "learn this project first" / "continue where we left off".
---

# Session Handover

## Overview

Sessions are marathons; the durable memory is files, not the context window. Every
project uses the same save/resume contract. Follow it exactly — a handover in
the wrong shape or place is one a fresh session never finds.

## Saving (end of session) — the file contract

Write exactly this set, then reply with the paths:

1. **`docs/handover/YYYY-MM-DD-session-handover.md`** — ONE file (never a separate
   session-notes file). Sections, in order:
   - `# Handover — <project> — YYYY-MM-DD`
   - `## Where we are` — 2–4 sentences: current state of branch/deploy/product NOW
   - `## Done this session` — what shipped, with file/branch/deploy references
   - `## Verify` — checkboxes for anything not re-checked after the last change
   - `## Open items` — each with status + the concrete next step
   - `## Next session: start here` — ordered; first item = the very next action
2. **`memory/MEMORY.md`** — update the rolling ledger: one line per changed topic
   linking to `memory/project_*.md` topic notes; update the notes whose facts changed.
   Create `memory/` if it doesn't exist.
3. If persona agents ran this session, their findings belong in
   `.claude/agent-memory/<agent>/MEMORY.md` — not in the handover.

CLAUDE.md is a static session primer — never append session state to it. Do not split
the handover across extra files or drop scratch files in the repo root.

**If time is short** (usage cap imminent, context nearly full): write at minimum
`## Where we are` and `## Next session: start here` in the correctly named file.
A partial handover in the right place beats a complete one that never gets written.

## Resuming (start of session) — the read order

1. `CLAUDE.md` (primer)
2. `memory/MEMORY.md` + the topic notes it links
3. Newest `docs/handover/*-session-handover.md`

Then, before doing anything else, state in one short paragraph where the project stands
and what the first action is.

## Common mistakes

| Wrong | Right |
|---|---|
| Two files (handover + session notes) | One dated session-handover file |
| Skipping `memory/MEMORY.md` | Ledger updated every save |
| Appending "latest session" to CLAUDE.md | CLAUDE.md stays static |
| Narrative transcript of the conversation | State + open items + next actions |
| Undated `HANDOVER.md` overwritten each time | Dated file per session (root `HANDOVER.md` only as a pointer if the project already has one) |
