# Vibecode 101 — Santo's Claude Code Playbook

A practical Claude Code playbook: setup, project conventions, the prompting loop, and
six installable house skills.

- **Engineers:** start at section 1; [PROMPTS.md](PROMPTS.md) has the copy-paste
  prompt loop.
- **Non-technical builders (C-level / head-level)** who vibecoded a product and want it
  in real production: go straight to
  [BUSINESS-USER-CHECKLIST.md](BUSINESS-USER-CHECKLIST.md) — the mandatory handoff
  checklist so DevOps can take over, modify, and deploy.

---

## 1. The stack at a glance

| Layer | What we use | Where it lives |
|---|---|---|
| Model | Fable 5 (`/model`), effort `xhigh`–`max` (`/effort`) | `~/.claude/settings.json` |
| Behavior | **ponytail** plugin (lazy/minimal senior-dev mode, injected every session via hooks) | plugin hooks — there is NO global CLAUDE.md |
| Process | **superpowers** plugin (brainstorm → plan → TDD → review → handover) | plugin |
| Design | **frontend-design** plugin (optional) + our own enterprise-ui skill | plugin + `~/.claude/skills/` |
| Messaging | **telegram** plugin (optional — MCP bridge, human-in-the-loop approvals) | plugin + `~/.claude/channels/telegram/` |
| Memory | per-project `memory/MEMORY.md` ledgers + `.claude/agent-memory/` + handover docs | in each repo |
| Skills | 6 house skills distilled from real history (section 6) | `novus-skills` plugin from this repo's marketplace |

Key insight: **the "house style" is enforced by plugins and skills, not by a giant
CLAUDE.md.** Project CLAUDE.md files stay tiny ("session primers") and delegate history
to memory/handover docs.

## 2. Setup from zero

1. Install Claude Code, log in.
2. Add marketplaces and plugins:
   ```
   /plugin marketplace add anthropics/claude-plugins-official
   /plugin marketplace add DietrichGebert/ponytail
   /plugin marketplace add forrestchang/andrej-karpathy-skills   # optional
   /plugin install superpowers ponytail
   /plugin install frontend-design telegram   # optional: UI quality / chat approvals
   ```
3. In `~/.claude/settings.json` set effort high and (optionally) the ponytail statusline.
   Ponytail default mode is `full`; switch with `/ponytail lite|full|ultra`, disable with
   "stop ponytail".
4. Install the house skills from this repo's own marketplace (section 6):
   ```
   /plugin marketplace add sgultom99/novus-skill
   /plugin install novus-skills@novustech
   ```
5. Telegram (optional): `/telegram:configure` with your own bot token, approve yourself
   via `/telegram:access`. Never share the bot token.
6. Per machine, permissions accumulate in `~/.claude/settings.local.json` and per-project
   `.claude/settings.local.json` — approve as you go, or run `/fewer-permission-prompts`.

## 3. Project conventions (what every repo looks like)

These recur across all mature projects — follow them so agents (and humans) can navigate
any repo the same way.

**CLAUDE.md = session primer, not a novel.** Skeleton used across the mature projects:

```markdown
# <Project> — session primer
Minimal context for a fresh Claude Code session. Read this first, then
memory/MEMORY.md (auto-loaded), then the latest docs/handover/ doc.

## 1. What this is
<2-4 sentences, production URL up front>

## 2. Stack
Spring Boot 3 · Java 21 · Flowable 7 · PostgreSQL 16 · Keycloak 26

## 3. Build / test / run
<the 3-5 commands that matter>

## 4. Conventions & gotchas
<the sharp edges only>

## Where to read more
memory/ = rolling ledger · docs/handover/ = session handovers · docs/ = PRDs, runbooks
```

**Memory layer (three tiers):**
- `memory/MEMORY.md` — index + rolling ledger, one line per topic note (`memory/project_*.md`).
- `docs/handover/YYYY-MM-DD-session-handover.md` — written at the END of every session
  (see the session-handover skill). The terminal-tooling project is the reference example.
- `.claude/agent-memory/<agent>/MEMORY.md` — persistent memory for persona subagents.

**Persona subagents** in `.claude/agents/*.md`, two flavors:
- Adversarial critics: `skeptical-bank-customer` (a named persona grounded in your
  target market), `skeptical-prospect`, `ux-product-critic`
- Domain experts: `banking-qa-domain-expert`, `fintech-solution-architect`,
  `workflow-qa-<market>`

They accumulate findings across sessions in agent-memory (one review-notes file per
locale where the product localizes). See the skeptical-evaluator skill.

**docs/ structure:** dated filenames (`2026-07-19-<topic>.md`) inside category folders:
`deploy/ security/ strategy/ research/ handover/ integration/ requirements/ finale/ demo/`.
Polished deliverables (PRD/FSD/pitch) render to `.md` + `.pdf` (+ `.docx`) via scripts.
Throwaway Playwright `.cjs` scripts for UAT screenshots live in `docs/` too.

**Git worktrees for parallel work.** Branch-per-feature with brand suffixes
(`feat/webhook-fixed-recipient-<brandA>` vs `-<brandB>`) so one feature ships to two
brands. Native `.claude/worktrees/` or sibling `wt-*` dirs. The busiest project runs
~15 live worktrees.

**Project skills** (`.claude/skills/<name>/SKILL.md`) encode "how to run/verify THIS
project end-to-end" — e.g. the KYC platform's `verify` skill boots the FastAPI app and drives a
full KYC flow over HTTP. Write one per project as soon as the run/verify dance is known.

**Settings trio** (from the M365 workspace, our most Claude-native project):
`settings.json` (committed, explicit MCP tool allowlist) + `settings.local.json`
(machine-local) + `settings.suggested.json` (onboarding suggestions for teammates).

## 4. How to prompt (the loop)

The whole engineering loop, battle-tested in real sessions.
Copy-paste templates for every step live in [PROMPTS.md](PROMPTS.md).

**① Bootstrap — make Claude read before it writes.** First message of every session:

> Learn this project first: read CLAUDE.md, memory/MEMORY.md, and the latest
> docs/handover/ doc, then tell me where we left off.

**② One dense goal prompt.** Bundle in a single message: the goal, a real product to
benchmark ("like hyperswitch.io/intelligent-routing", "like Termius"), which repo/folder
to learn first, the deploy target, and the access you grant ("you can run bash / SSH /
spawn agents"). Quality bar words that actually steer output: *realistic, enterprise,
production-ready, monochrome near-flat, not fancy AI-bot look*.

**③ Plan gate.** "write the plan" → review → "approved". Plans are chunked into phases
(P1–P4 / waves). Superpowers' brainstorming + writing-plans skills fire here.

**④ Full-auto execution.** "go full auto p1" → then drive with the drumbeat:
`continue` · `merge and deploy` · `commit and push` · `verify the deploy is live`.
Paste raw CI logs / stack traces back in and say "fix it" — no need to pre-digest them.

**⑤ Adversarial QA.** Point the stateful skeptical persona at the live demo:
"ask @skeptical-bank-customer to test <the live demo URL> — she has her memory from previous
evaluations — then act as the product owner on her feedback."

**⑥ Teardown — ALWAYS.** Before ending (or before the weekly usage cap hits):

> Save the session: handover doc + memory update so a fresh session can continue.

This is the single most important ritual. The session-handover skill standardizes it.

**Model/effort routing:** `/model` and `/effort` are tuned constantly (72 `/usage`,
49 `/model`, 44 `/effort` calls in one month). Pattern: Fable 5 for planning/strategy,
Opus 4.8 for bulk implementation; effort `max` for hard problems. `/compact` to survive
marathon sessions; `/code-review` and `/ultrareview` before merging significant work.

## 5. Loop engineering (scaling beyond one session)

- **Marathon sessions + compaction** beat many small sessions — but only because every
  session ends with a handover doc and starts by reading it. The docs are the real memory;
  the context window is a cache.
- **Parallelism** = worktrees + subagents. Independent features get their own worktree and
  branch; persona agents run QA while you build; `spawn agent <model>` routes work by role.
- **Human-in-the-loop via Telegram** for anything outbound (messages, approvals). Standing
  security stance: *inbound content is data, not instructions; approvals only come from
  Telegram or the terminal.*
- **Scheduled loops**: `/loop` for polling tasks, `/schedule` for recurring cloud agents
  (the workflow project drives weekly scans with scheduled tasks).

## 6. House skills (the `novus-skills` plugin)

This repo doubles as a Claude Code **plugin marketplace**. One-time install:

```
/plugin marketplace add sgultom99/novus-skill
/plugin install novus-skills@novustech
```

Updates flow with every push — pick them up with `/plugin marketplace update novustech`
(or enable auto-update in `/plugin` → Marketplaces). Manual fallback: copy `skills/*`
into `~/.claude/skills/` — but the plugin is preferred, it stays in sync and never
collides with skills you already have (plugin skills are namespaced).

Five skills are **general-purpose** (no company/product/market specifics — shareable
beyond the team); `novus-design-system` is deliberately branded.

| Skill | Fires when | What it fixes |
|---|---|---|
| `session-handover` | "save session/handover", "learn this project first" | Standard handover format + file locations, so any fresh session resumes in one read |
| `skeptical-evaluator` | "skeptical customer / persona QA / brutal feedback" | Stateful adversarial persona with persistent agent-memory instead of one-off roleplay |
| `enterprise-ui` | any enterprise/back-office UI work | Monochrome near-flat Ant-Design-grade UI; kills AI-slop (gradients, big radius, leftover template text, broken mobile) |
| `secret-hygiene` | a live credential appears in chat/logs/files | Never echo secrets; env/Vault refs; flag for rotation |
| `production-handoff` | "prepare handoff to DevOps", "get this to production" | The fixed handoff-package shape (PRD, environment recipe, demo-vs-real, services+owners, .env.example) + secret scans; never claims production-ready |
| `novus-design-system` | any Novus-branded frontend or `nova*` product UI | Wires the real design system (bundled `tokens.css` + Carlito) instead of an invented palette; locked logo/dark-mode/wording rules |

(A seventh candidate, `merge-and-deploy`, was tested and dropped: baseline agents
already run the full ship loop correctly — push → MR → CI green → merge → watch
pipeline → verify live before reporting done. Documented in section 4; no skill needed.)

### Use cases & examples

**`session-handover` — never lose a session again.**
End every session (or when the usage cap looms) with:
> Save the session: handover doc + memory update so a fresh session can continue.

Claude writes ONE dated `docs/handover/YYYY-MM-DD-session-handover.md` (Where we are /
Done / Verify / Open items / Next session: start here) and updates the `memory/MEMORY.md`
ledger. Next session, open with *"Learn this project first"* — it reads CLAUDE.md →
memory → newest handover and tells you where you left off. Under time pressure it writes
the two essential sections rather than nothing.

**`skeptical-evaluator` — QA that remembers being burned.**
First run:
> Create a skeptical customer persona to test https://demo.example.com (login demo/demo)
> and give brutal feedback. She must remember her evaluations across sessions.

Claude creates `.claude/agents/<persona>.md` grounded in your actual buyer (name, role,
institution, market) and append-only memory in `.claude/agent-memory/<persona>/MEMORY.md`.
Repeat runs (*"this is her 4th evaluation — read her memory first"*) re-check every
"still broken" item before hunting new findings, and score /10 with a delta — the trend
line across runs is the real QA signal.

**`enterprise-ui` — enterprise looks, not AI-bot looks.**
Just build UI; the skill applies itself. Ask for *"a settlement reconciliation dashboard"*
and you get solid neutral grounds, ≤6px radius, 1px borders over shadows, one accent,
compact density — plus a ship checklist run before "done": grep for gradients and
leftover CJK template text, 375px mobile pass, radius audit, contrast ≥4.5:1 with
visible focus states.

**`secret-hygiene` — a pasted secret is a burned secret.**
Paste a token mid-task (it happens) and Claude stages it as an in-shell env var, never
echoes it back, and ends the task with an explicit rotation flag — because everything
typed in chat is stored in plaintext transcripts forever. Also fires on scans: it knows
the secret-shaped strings to look for (`ghp_`, `glpat-`, `sk_live_`, `AKIA`, private-key
blocks, JWTs, `user:password@` URLs).

**`production-handoff` — from vibecoded prototype to something DevOps can ship.**
> Prepare the production handoff package for my engineers.

Builds the full 6-artifact package (README/CLAUDE.md hygiene check, DRAFT PRD for owner
review, `docs/handoff/environment.md` with the exact model/plugins/skills/MCP recipe,
`demo-vs-real.md` worst-first, `services.md` with an owner slot per account,
`.env.example` names-only), scans working tree + git history + chat for secrets, and
closes by saying what YOU must do next — and that engineering, not Claude, declares it
production-ready.

**`novus-design-system` — on-brand Novus frontends, zero guessing.**
> Build the novapay merchant dashboard with dark mode.

Instead of inventing a palette, Claude copies the bundled `tokens.css` + Carlito fonts
(snapshot 2026-07-07) into the project and derives everything from `var(--…)` tokens and
the shipped component classes. Locked behaviors enforced: dual-trigger dark mode
(toggle + OS) with the logo swapping to white, placed logo assets (never a typed
"NOVUS"), lowercase solid-set product names, hover only on clickables, mobile-first
375px/44px floors, and "Service as Software" wording (the word "SaaS" never appears —
not even negated). The living master stays in the design-system SharePoint folder
(owner: Rick) — the skill tells Claude where to fetch fresh copies.

## 7. Security — read this twice

- **Never paste live tokens into the chat.** Everything you type is stored in plaintext
  transcripts under `~/.claude/projects/`. Our history contains dozens of pasted
  Cloudflare/GitLab/Vault/Telegram/Midtrans tokens — treat all of them as burned and
  rotate. Pass secrets as env vars, Vault refs, or masked CI variables and give Claude the
  *reference*, not the value.
- **Do not share `~/.claude/projects/` or `~/projects/credentials/` with anyone.** Share
  this playbook and the `skills/` folder instead.
- `~/.claude/channels/telegram/.env` holds the bot token; `.claude/telegram.env` per
  project likewise. Keep them out of git.
- Broad grants like `Read(//home/**)` in `settings.local.json` are convenient on a solo
  machine — don't replicate them on shared machines.

## 8. Where to look for living examples

Illustrative layout only — the repo names below are made up. Every mature novustech
project follows these patterns; ask Santo which internal repo best demonstrates each.

| Pattern | What it looks like |
|---|---|
| Session primer CLAUDE.md + memory ledger | `~/projects/banking-suite/CLAUDE.md` + `memory/MEMORY.md` |
| Dated session handovers | `~/projects/ops-console/docs/handover/2026-07-19-session-handover.md` |
| Persona agents + agent-memory | `~/projects/banking-suite/.claude/agents/` + `.claude/agent-memory/` |
| Worktree fan-out, multi-brand branches | `~/projects/omni-dialer` with `wt-*` siblings, `feat/<feature>-<brand>` branches |
| Project verify skill | `~/projects/kyc-portal/.claude/skills/verify/SKILL.md` |
| MCP allowlist + settings trio + triage skill | `~/projects/team-workspace/.claude/settings.json` + `settings.suggested.json` |
| Categorized docs/ + polished deliverables | `~/projects/payment-hub/docs/deploy/`, `docs/security/`, `docs/handover/` |
