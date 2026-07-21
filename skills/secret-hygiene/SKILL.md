---
name: secret-hygiene
description: Use whenever a live credential appears in the conversation or the work — an API token, PAT, bot token, private key, or password pasted in chat, found in a file, or printed in logs/CI output — before using, storing, or echoing it.
---

# Secret Hygiene

## Overview

Everything typed in this chat is stored in plaintext session transcripts on disk
(`~/.claude/projects/`). **A secret pasted in chat is an exposed secret.** You may
still use it to do the job — but the exposure must be closed out, not ignored.

## Required steps when a secret lands in chat

1. **Use it without spreading it**: stage it as an env var in-shell (leading space to
   skip history) or a `chmod 600` file outside the repo. Never in command-line
   arguments (`ps` leaks them), never in tracked files, YAML, docs, or scripts.
2. **Never echo it back** — in replies, summaries, commit messages, or code comments.
   Refer to it as `<API_TOKEN>` / `$DEPLOY_TOKEN`.
3. **Give it a durable home** if it must persist: Vault reference, masked+protected CI
   variable, or an untracked `.env` — and point config at the reference, not the value.
4. **REQUIRED — close out the exposure.** Every task that consumed a pasted secret ends
   with an explicit line in your final reply:
   > This token passed through chat and is stored in the session transcript on disk —
   > rotate it (or let it expire) once the task is verified. Next time, prefer a
   > short-lived scoped token or an env/Vault reference instead of the raw value.
   This line is part of "done". A deploy that works but skips it is unfinished.
5. **Secrets found already committed** (in git history, transcripts, or docs): deleting
   the file does not un-leak it — flag for rotation.

## Secret-shaped strings (what to scan for)

When scanning a repo, diff, or log for exposed credentials, look for at least:
provider token prefixes (`ghp_`, `glpat-`, `sk-`, `sk_live_`, `AKIA`, `hvs.`,
`xox`), `-----BEGIN … PRIVATE KEY-----` blocks, `eyJ…` JWTs, connection strings
and URLs with embedded `user:password@`, and `password=` / `token=` / `secret=`
assignments with a literal value.

## Quick reference

| Situation | Action |
|---|---|
| Token pasted in chat | Steps 1–4: stage in-shell, use, durable home, flag rotation |
| Token visible in CI log output the user pasted | Point it out + flag rotation; mask the CI variable |
| Secret hard-coded in repo | Move to env/Vault ref + flag rotation (history keeps it) |
| Asked to write a secret into a committed file | Refuse the location; offer untracked `.env`/Vault/CI variable |
