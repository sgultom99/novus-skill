# Vibecode 101 — Santo's Claude Code Playbook

Onboarding guide for teammates joining the novustech company portfolio (1nCall, 1nFlow,
the Keycloak-based SSO platform, 1pay2link, IDHub360, TermSSH, SoftPOS, iForte routing
engine).

**Non-technical builder (C-level / head-level) who vibecoded a product and wants it in
real production?** Skip to [BUSINESS-USER-CHECKLIST.md](BUSINESS-USER-CHECKLIST.md) —
the mandatory handoff checklist so DevOps can take over, modify, and deploy. Engineers:
keep reading here, and [PROMPTS.md](PROMPTS.md) has the copy-paste prompt loop. Everything here
was extracted from the real setup in `~/.claude` and the real session history of ~11
shipped projects (June–July 2026). Copy this setup and you inherit the whole workflow.

---

## 1. The stack at a glance

| Layer | What we use | Where it lives |
|---|---|---|
| Model | Fable 5 (`/model`), effort `xhigh`–`max` (`/effort`) | `~/.claude/settings.json` |
| Behavior | **ponytail** plugin (lazy/minimal senior-dev mode, injected every session via hooks) | plugin hooks — there is NO global CLAUDE.md |
| Process | **superpowers** plugin (brainstorm → plan → TDD → review → handover) | plugin |
| Design | **frontend-design** plugin + our own enterprise-ui skill | plugin + `~/.claude/skills/` |
| Messaging | **telegram** plugin (MCP bridge, human-in-the-loop approvals) | plugin + `~/.claude/channels/telegram/` |
| Cloud | **aws-startup-advisor** plugin (2 MCP servers: AWS knowledge + pricing) | plugin |
| Memory | per-project `memory/MEMORY.md` ledgers + `.claude/agent-memory/` + handover docs | in each repo |
| Skills | 5 house skills distilled from real history (section 6) | `~/.claude/skills/` |

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
   /plugin install superpowers ponytail telegram frontend-design aws-startup-advisor
   ```
3. In `~/.claude/settings.json` set effort high and (optionally) the ponytail statusline.
   Ponytail default mode is `full`; switch with `/ponytail lite|full|ultra`, disable with
   "stop ponytail".
4. Copy the `skills/` directory from this repo into `~/.claude/skills/` (section 6).
5. Telegram (optional): `/telegram:configure` with your own bot token, approve yourself
   via `/telegram:access`. Never share the bot token.
6. Per machine, permissions accumulate in `~/.claude/settings.local.json` and per-project
   `.claude/settings.local.json` — approve as you go, or run `/fewer-permission-prompts`.

## 3. Project conventions (what every repo looks like)

These recur across all mature projects — follow them so agents (and humans) can navigate
any repo the same way.

**CLAUDE.md = session primer, not a novel.** Skeleton used in 1nCall/1nFlow/1pay2link:

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
  (see the session-handover skill). TermSSH is the reference example.
- `.claude/agent-memory/<agent>/MEMORY.md` — persistent memory for persona subagents.

**Persona subagents** in `.claude/agents/*.md`, two flavors:
- Adversarial critics: `skeptical-bank-customer` ("Ibu Ratna Wijaya"), `skeptical-indonesian-prospect`, `ux-product-critic`
- Domain experts: `banking-qa-domain-expert`, `fintech-solution-architect`, `workflow-qa-indonesia`

They accumulate findings across sessions in agent-memory (per-locale review notes for
id/my/vn/th/lk/ph). See the skeptical-evaluator skill.

**docs/ structure:** dated filenames (`2026-07-19-<topic>.md`) inside category folders:
`deploy/ security/ strategy/ research/ handover/ integration/ requirements/ finale/ demo/`.
Polished deliverables (PRD/FSD/pitch) render to `.md` + `.pdf` (+ `.docx`) via scripts.
Throwaway Playwright `.cjs` scripts for UAT screenshots live in `docs/` too.

**Git worktrees for parallel work.** Branch-per-feature with brand suffixes
(`feat/webhook-fixed-recipient-1ncall` vs `-novus`) so one feature ships to two brands.
Native `.claude/worktrees/` or sibling `wt-*` dirs. 1nCall runs ~15 live worktrees.

**Project skills** (`.claude/skills/<name>/SKILL.md`) encode "how to run/verify THIS
project end-to-end" — e.g. IDHub360's `verify` skill boots the FastAPI app and drives a
full KYC flow over HTTP. Write one per project as soon as the run/verify dance is known.

**Settings trio** (from the M365 workspace, our most Claude-native project):
`settings.json` (committed, explicit MCP tool allowlist) + `settings.local.json`
(machine-local) + `settings.suggested.json` (onboarding suggestions for teammates).

## 4. How to prompt (the loop)

The whole engineering loop, as practiced across ~200 sampled real prompts.
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
"ask @skeptical-bank-customer to test demo.1nflow.ai — she has her memory from previous
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
  (1nFlow uses scheduled tasks).

## 6. House skills (in this repo under `skills/`, install to `~/.claude/skills/`)

The copies in `skills/` are **general-purpose** — no company, product, or market
specifics — so they can be shared beyond the team and dropped into any project as-is.

| Skill | Fires when | What it fixes |
|---|---|---|
| `session-handover` | "save session/handover", "learn this project first" | Standard handover format + file locations, so any fresh session resumes in one read |
| `skeptical-evaluator` | "skeptical customer / persona QA / brutal feedback" | Stateful adversarial persona with persistent agent-memory instead of one-off roleplay |
| `enterprise-ui` | any UI work in our projects | Monochrome near-flat Ant-Design-grade UI; kills AI-slop (gradients, big radius, leftover Chinese template text, broken mobile) |
| `secret-hygiene` | a live credential appears in chat/logs/files | Never echo secrets; env/Vault refs; flag for rotation |
| `production-handoff` | "prepare handoff to DevOps", "get this to production" | The fixed handoff-package shape (PRD, environment recipe, demo-vs-real, services+owners, .env.example) + secret scans; never claims production-ready |

(A sixth skill, `merge-and-deploy`, was tested and dropped: baseline agents already run
the full ship loop correctly — push → MR → CI green → merge → watch pipeline → verify
live before reporting done. The expectation is documented in section 4; no skill needed.)

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

| Pattern | Best example |
|---|---|
| Session primer CLAUDE.md + memory ledger | `~/projects/banks/1nflow` |
| Dated session handovers | `~/projects/toolbox/termssh/docs/handover/` |
| Persona agents + agent-memory | `~/projects/banks/1nflow/.claude/` |
| Worktree fan-out, multi-brand branches | `~/projects/ai/novusflow` (1nCall) |
| Project verify skill | `~/projects/kyc/truecdd/.claude/skills/verify/` |
| MCP allowlist + settings trio + triage skill | `~/projects/toolbox/microsoft-teams` |
| Categorized docs/ + polished deliverables | `~/projects/ifortepay/payment-orchestrator/docs/` |
