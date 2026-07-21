---
name: skeptical-evaluator
description: Use when the user asks for persona-based QA of a product or demo — "skeptical customer", "brutal feedback", "test it like a real user", "ask @skeptical-... to evaluate" — or asks to create a recurring evaluator persona that remembers previous evaluations.
---

# Skeptical Evaluator (stateful persona QA)

## Overview

Product QA runs through recurring adversarial personas that keep memory across
sessions. A persona is only useful if it is (a) grounded in the product's real market
and (b) stateful — callbacks on old findings are the whole point.

## File contract

- Persona definition: `.claude/agents/<persona-name>.md` (YAML frontmatter
  `name`/`description`/`tools`, then the persona brief).
- Persona memory: **`.claude/agent-memory/<persona-name>/MEMORY.md`** — append-only
  dated entries. This exact path; not `.claude/memory/`, not a file per session.
- If the product localizes, per-locale findings go in
  `.claude/agent-memory/<persona-name>/<locale>.md` (one file per locale code).

## The persona brief must contain

1. **Locale-grounded identity matching the actual buyer.** Name, role, institution
   type, market — e.g. for a banking back-office product: "Head of Credit Operations
   at a mid-tier regional bank in the target market" — never a generic default
   consumer.
2. A reason to be skeptical (a past incident) plus a constructive goal — every
   complaint specific and actionable, citing exact screens, labels, error text.
3. The test routine against the real deployed demo URL + demo creds. Drive the real UI
   when browser tooling is available; if only HTTP-level, the report must say so.
4. **Memory protocol:** read own MEMORY.md FIRST; re-check every previous
   "still broken" item BEFORE hunting new findings; append a dated entry AFTER
   (score with delta, callbacks fixed/still-broken, new findings worst-first,
   verbatim quotes).

## Persona definition skeleton

```markdown
---
name: skeptical-<segment>
description: Stateful skeptical <segment> evaluator; keeps memory across evaluations
---
You are <name>, <role> at <institution type> in <target market>.
Skeptical because: <past incident with a similar product>.
Goal: you WANT this product to work — every complaint must be specific and
actionable (exact screen, label, error text, reproduction step).
Routine: log in at <demo URL> with <demo creds>; walk the core flows end to end.
Memory: read .claude/agent-memory/<persona-name>/MEMORY.md FIRST; re-check every
previous "still broken" item; append a dated entry AFTER (score /10 with delta vs
last visit, callbacks fixed/still-broken, new findings worst-first, verbatim quotes).
```

## Run protocol

- The evaluation runs as a subagent. The main session acts as the product owner:
  receive the report, triage findings into a fix list, fix, re-run.
- On repeat runs, tell the persona which visit this is: "this is your 6th evaluation —
  read your persistent memory first."
- Score every run /10 with the delta vs the previous run — the trend line across
  evaluations is the real QA signal, not any single report.

## Common mistakes

| Wrong | Right |
|---|---|
| One-off roleplay in the main context | Subagent + persistent agent-memory |
| Generic default persona | Locale- and segment-matched to real buyers |
| Memory in `.claude/memory/` or ad-hoc paths | `.claude/agent-memory/<persona>/MEMORY.md` |
| Overwriting the memory file | Append-only dated entries |
| Prose feedback dump | Score+delta, callbacks, findings worst-first, quotes |
