---
name: production-handoff
description: Use when a business user or non-technical builder asks to prepare their vibecoded app for handoff to DevOps/engineering — "prepare for handoff", "package this for deployment", "this is ready, get it to production", "my engineers will take over" — before creating any handoff documents.
---

# Production Handoff (business user → DevOps)

## Overview

The deliverable is a **handoff package**, not a deployment. DevOps will modify and ship
it; your job is to make the folder self-explanatory to an engineer who has never met
the builder. The package has a fixed shape — a handoff missing the environment recipe
or the demo-vs-real file becomes a meeting instead of a deployment.

## The package contract (create/refresh ALL of these)

1. **Base hygiene** — verify and fix before writing anything new: git repo with
   committed history; `README.md` with plain-language what-it-is + how-to-run;
   `CLAUDE.md` session primer; latest `docs/handover/` doc; `memory/MEMORY.md` current.
2. **`docs/PRD.md`** — the business intent in the USER'S language: what it must do,
   who uses it, what "correct" means. Draft it from session knowledge, mark it
   `DRAFT — review by owner`, and ask the user to review. Engineers cannot infer this
   from code; it is not optional.
3. **`docs/handoff/environment.md`** — how this was built, so DevOps can reproduce the
   agent setup: model(s) and effort level used, plugins installed, custom skills used
   (project `.claude/skills/` and user-level), MCP servers (`.mcp.json`), notable
   permissions granted. Note that optional prompt history lives under
   `~/.claude/projects/<dash-encoded-path>/` and must be secret-scrubbed before sharing.
4. **`docs/handoff/demo-vs-real.md`** — everything mocked, hardcoded, demo-only,
   sandbox-mode, untested, or known-broken; plus what was actually exercised by a
   human. Worst first.
5. **`docs/handoff/services.md`** — every external service touched (hosting, domain/
   DNS, payments incl. sandbox-vs-live, email/SMS, third-party APIs) with an
   `owner: <name, email>` slot per service for the user to fill in.
6. **`.env.example`** — every env var the code reads, names and placeholders only.

## Required scans (before declaring the package done)

- Secret scan: working tree AND git history for secret-shaped strings; check
  `.gitignore` actually excludes `.env*` and local DB files (a rule over an
  already-tracked file does nothing).
- Recall the conversation: any credential the user ever pasted in chat gets named
  (by reference, never value) in the final reply with a rotation instruction.

## The final reply must contain

1. The path of each of the six package artifacts (created or refreshed).
2. Rotation flags for every credential exposed in chat or history — by reference,
   never the value.
3. What the user must do next: review the DRAFT PRD, fill in service owners, hand
   credentials over via a secure channel (password manager / vault — never the repo).
4. The explicit statement that engineering, not the builder and not the assistant,
   declares the app production-ready.

## Boundaries

- No CI/CD, Docker, or infra scaffolding — DevOps owns those choices. Document the
  app's real requirements (env vars, build/start commands, port) instead.
- No silent security fixes (hardcoded logins, sandbox keys) — list them in
  demo-vs-real with a recommendation; changing auth or payment mode is a decision.
- Never claim the app is production-ready. Engineering declares that after their
  review — say so in the final reply.

## Common mistakes

| Wrong | Right |
|---|---|
| One ad-hoc root `HANDOFF.md` | The 6-item contract above, in `docs/` |
| Skipping the environment recipe | Model/plugins/skills/MCP recorded — DevOps must reproduce the setup |
| "Open decisions" list without owners | `services.md` with an owner slot per account |
| Secrets scan of the working tree only | Tree + git history + chat recall with rotation flags |
| Handing off without a PRD | Draft PRD from session knowledge, marked for owner review |
