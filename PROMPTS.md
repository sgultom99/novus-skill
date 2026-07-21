# Prompt Cookbook — the loop, ready to copy-paste

Cleaned-up templates of the prompts actually used across the novustech portfolio
(mined from ~200 real session prompts, June–July 2026). Replace `<...>` slots.
Typos are fine — Claude parses intent — but never paste a live secret (see README §7).

## 1. Session bootstrap (first message, every session)

```
Learn this project first: read CLAUDE.md, memory/MEMORY.md and the topic notes it
links, and the latest doc in docs/handover/. Then tell me in one short paragraph
where we left off and what the first action is. Don't change anything yet.
```

## 2. The dense goal prompt (new feature / new product)

One message, five slots — goal, benchmark, context source, target, granted access:

```
/goal I want <what to build> like <real product to benchmark, with URL> --
learn first from <repo/folder/doc to read> -- this ships to <deploy target /
production URL> -- make it realistic, enterprise, production-ready, monochrome
near-flat UI (no fancy AI-bot look) -- I allow you bash shell execution and
you can spawn agents; run it directly, I'll add the permission
```

## 3. Plan gate → phased execution

```
write the plan          → review it →
approved, go full auto p1
continue full auto p2
continue full auto p3 and p4
```

Plans are chunked into phases (P1–P4 / waves) so you can approve once and drive
with one-liners. If the plan looks wrong, fix it BEFORE "approved" — that's the
cheap moment.

## 4. The drumbeat (while executing)

| You type | Claude does |
|---|---|
| `continue` | next step of the approved plan |
| `commit and push` | commit, push, report |
| `merge and deploy` | merge → CI green → deploy → **verify live** → report |
| `verify the deploy is live` | hit the prod URL / rollout status, prove it |
| *(paste raw CI log / stack trace)* `fix it` | root-cause and fix — no need to pre-digest logs |

## 5. Adversarial QA (stateful persona)

First run:

```
Create a skeptical <segment, e.g. Indonesian bank customer> persona to test
<demo URL> (login <demo creds>) and give brutal feedback. She must remember her
evaluations across sessions. (The skeptical-evaluator skill defines the file layout.)
```

Repeat runs:

```
Ask @<persona-name> to test <demo URL> again — this is her <N>th evaluation, she
must read her persistent memory first and re-check what was still broken. Then
you act as the product owner: triage her findings into a fix list and start fixing.
```

## 6. Session teardown (ALWAYS, before ending or near the usage cap)

```
Save the session: handover doc + memory update so a fresh session can continue
with just the handover. (The session-handover skill defines the exact files.)
```

## 7. Model / effort routing cheatsheet

- `/model` — Fable 5 for planning, strategy, hard debugging; Opus 4.8 for bulk
  implementation. You can also route per-subagent: "spawn a Fable 5 agent for the
  plan, Opus 4.8 agents for implementation."
- `/effort` — `max` for hard problems, default `xhigh` otherwise.
- `/usage` — check burn rate; save a handover before the weekly cap hits.
- `/compact` — survive marathon sessions; safe because the docs are the real memory.
- `/code-review` (or `/code-review ultra`) before merging significant work.

## 8. Don'ts (each one is a lesson paid for in real sessions)

- Don't paste live tokens/keys — transcripts store them in plaintext forever.
  Give a reference (env var name, Vault path, masked CI variable) instead.
- Don't skip the teardown prompt — a session without a handover is a session
  the next context window can't continue.
- Don't accept the first UI — check against the enterprise-ui skill checklist
  (gradients, radius, leftover Chinese template text, mobile).
- Don't merge without the plan gate on big work — one-line approvals only work
  because the plan was reviewed once at the start.
