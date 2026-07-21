# Business User → Production Checklist

Mandatory guideline for C-level / head-level builders who vibecode a product with
Claude Code knowing only the product and business context — and want it deployed to
real production. You do NOT deploy to production yourself. You produce a **handoff
package** from this checklist; DevOps/engineering takes it from there, modifies it,
and owns the production deployment.

Rule of thumb: if every box below is ticked, an engineer who has never spoken to you
can pick up the folder and ship it. If a box is not ticked, that becomes a meeting.

---

## A. One-time setup (do this before your first session)

- [ ] Claude Code installed and logged in.
- [ ] Model: `/model` → **Fable 5** for planning/strategy, Opus 4.8 is fine for bulk
      building. Note which one you used (goes in the handoff).
- [ ] Effort: `/effort` → high.
- [ ] Plugins (one time): `superpowers` (plan → build → review process),
      `frontend-design` (UI quality). See README §2 for install commands.
- [ ] House skills copied from this repo's `skills/` into `~/.claude/skills/` —
      especially `session-handover` (saves your progress for the next session) and
      `secret-hygiene` (protects you from leaking credentials).
- [ ] MCP servers: only add what the product truly needs. Every MCP server you add is
      something DevOps must also install — write down each one you add and why.

## B. Rules during every session (non-negotiable)

- [ ] **Never paste passwords, API keys, or tokens into the chat.** Everything you
      type is saved to disk forever. Say "I'll provide the key as an env variable"
      instead. If you already pasted one: tell your engineer, it must be rotated.
- [ ] **One project = one folder.** All work in a single folder under version control —
      ask Claude: *"git init and commit after every working change."*
- [ ] **Plan gate:** for anything non-trivial, say *"write the plan"*, read it in
      business terms, then *"approved"*. Never approve a plan you didn't read.
- [ ] **End every session** with: *"Save the session: handover doc + memory update so
      a fresh session can continue."* A session without this is lost work.
- [ ] Ask for **demo vs real** honesty: *"list everything that is mocked, hardcoded, or
      demo-only in this app"* — keep that list current; it goes in the handoff.

## C. The handoff package (the mandatory checklist)

Everything below must exist inside the project folder before you hand it to DevOps.
Shortcut: run the copy-paste prompt in section E and Claude assembles it for you.

**1. Code & docs**
- [ ] Git repository with committed history (not a loose folder of files).
- [ ] `README.md` — what the product is, how to run it locally, in plain language.
- [ ] `CLAUDE.md` — the session primer (stack, build/run commands, gotchas).
- [ ] `docs/handover/` — latest dated session-handover doc (state, open items, next steps).
- [ ] `memory/MEMORY.md` — the rolling ledger of decisions and facts.
- [ ] Business requirements: a `docs/PRD.md` (or similar) in YOUR words — what it must
      do, who uses it, what "correct" means. Engineers can't infer this from code.

**2. The environment recipe (so DevOps can reproduce your setup)**
- [ ] `docs/handoff/environment.md` listing: **model(s) used**, effort level,
      **plugins installed**, **custom skills used** (from `~/.claude/skills/` or the
      project's `.claude/skills/`), **MCP servers configured** (`.mcp.json` if any),
      and any special permissions you granted.

**3. External services & access**
- [ ] List of every external service the app touches: domains/DNS, hosting, payment
      provider (and whether sandbox or live!), email/SMS, third-party APIs — with
      **who owns each account** (name + email).
- [ ] `.env.example` with variable NAMES only, never values.
- [ ] Actual credentials handed over via password manager / Vault / in person —
      **never inside the repo, docs, or chat.**

**4. Honesty section**
- [ ] `docs/handoff/demo-vs-real.md` — everything mocked, hardcoded, demo-only,
      untested, or known-broken. This is the most valuable file in the package;
      an incomplete one costs your engineers weeks.
- [ ] Statement of what was actually tested (by you, clicking through) vs never tried.

**5. Prompt history (OPTIONAL — share only if asked, and only scrubbed)**
- [ ] Your full conversation history lives at `~/.claude/projects/<project-path-with-
      slashes-turned-into-dashes>/` as `.jsonl` files. It shows engineers exactly how
      and why everything was built — genuinely useful context.
- [ ] **But**: transcripts contain everything you ever typed, including any secrets
      you pasted. Before sharing, have Claude scan them for credentials, and rotate
      anything found. When in doubt, share the handover docs instead — they carry the
      same knowledge without the risk.

## D. Handing it over

- [ ] Give DevOps: the git repo, the credentials via a secure channel, and a 30-minute
      walkthrough of the PRD + demo-vs-real list.
- [ ] Expect them to **change things**. Vibecoded output is a working prototype plus
      intent; DevOps adds CI/CD, environments, secrets management, monitoring,
      backups, and security review. That is normal, not criticism.
- [ ] **"Production-ready" is declared by engineering, not by you or by Claude.**
      Do not point real customers, real money, or real personal data at the app
      before that sign-off.

## E. Copy-paste prompt to assemble the package

If you installed the house skills (section A), the `production-handoff` skill fires on
"prepare this for handoff to devops" and builds the package automatically. Otherwise,
run this in your project at the end, and Claude builds the checklist artifacts:

```
Prepare the production handoff package for DevOps. Check this project against
BUSINESS-USER-CHECKLIST.md section C (in the vibecode101 playbook repo): make sure
git history, README.md, CLAUDE.md, latest docs/handover/ doc and memory/MEMORY.md
exist and are current. Then create: docs/PRD.md from what you know of the business
intent (I will review it), docs/handoff/environment.md (model, effort, plugins,
skills, MCP servers, permissions used in this project), docs/handoff/demo-vs-real.md
(everything mocked, hardcoded, demo-only, or untested), and .env.example with
variable names only. Scan the repo for any committed secrets and tell me if any
credential ever appeared in our chats so I can rotate it. Show me the list of
external services and who owns each account so I can fill in the owners.
```
