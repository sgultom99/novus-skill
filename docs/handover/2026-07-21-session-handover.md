# Handover — vibecode101 — 2026-07-21

## Where we are

The repo is live at `github.com:sgultom99/novus-skill.git` (main @ `17dfa5d`) and now
serves three roles at once: teammate playbook (README, PROMPTS, BUSINESS-USER-CHECKLIST),
skill source (`skills/`, six TDD-verified skills), and installable Claude Code plugin
marketplace (marketplace `novustech`, plugin `novus-skills`, no version field so every
push is a new version). All six skills are also installed locally in `~/.claude/skills/`.

## Done this session

- Generalized the five original skills (brand/product/market terms removed, grep-verified)
  and strengthened each: time-short minimum handover, persona skeleton + score-delta,
  CSS tokens + accessibility floor, secret-shaped-strings scan list, required final-reply
  shape for handoff. Three simulation verifications passed (`skills/*/SKILL.md`).
- Pushed the repo to GitHub (root commit `0161c85`; GitHub host keys verified by
  fingerprint before first push).
- Built `novus-design-system` (`skills/novus-design-system/`): locked values/behaviors
  distilled from the design-system SharePoint kit, bundled `tokens.css` + Carlito woff2
  snapshot (2026-07-07). Fetched via `az` + Microsoft Graph — the M365 tenant
  (`1c50ab90…`) needs an explicit `--tenant` token; the default az tenant fails with
  "Tenant does not have a SPO license". Full TDD: RED (agent invented a parallel brand),
  GREEN pass, one loophole ("not SaaS" tagline) closed and re-tested.
- Made the repo a plugin marketplace (`.claude-plugin/marketplace.json` + `plugin.json`,
  `claude plugin validate .` passes) — commit `cc3ec72`.
- Rewrote README §6 around the marketplace install with per-skill use cases + examples;
  updated stack table, setup step, and the business-user checklist — commit `17dfa5d`.

## Verify

- [ ] End-to-end marketplace test from a fresh session:
      `/plugin marketplace add sgultom99/novus-skill` →
      `/plugin install novus-skills@novustech` → skills fire. Never run live.
- [ ] GitHub repo visibility is **private** — it now contains internal branding, design
      tokens, and the SharePoint location.

## Open items

- **Credential rotation (urgent, user-side):** old session transcripts under
  `~/.claude/projects/` hold pasted Cloudflare/GitLab/Vault/Telegram/Midtrans tokens —
  all still flagged as burned; rotate them.
- **`git push` is blocked for Claude** by the auto-mode permission classifier — Santo
  pushes with `! git push`. Optionally add a Bash allow rule for `git push` in settings.
- **Design-system snapshot drift:** the bundled tokens/fonts are the 2026-07-07 snapshot;
  refresh from the SharePoint master (owner: Rick) when the kit changes, and bump the
  date in the skill.

## Next session: start here

1. Run the end-to-end marketplace install test from a fresh session (Verify box 1).
2. Confirm the GitHub repo is private (Verify box 2).
3. When teammates onboard, collect their friction points on the skills/playbook and
   iterate — the plugin means fixes reach them on the next `marketplace update`.
