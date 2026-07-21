# Handover — vibecode101 — 2026-07-21

## Where we are

Repo fully pushed (`main` @ `a3295f3`, working tree clean) and serving three roles:
teammate playbook (README, PROMPTS, BUSINESS-USER-CHECKLIST), skill source (`skills/`,
six TDD-verified skills), and installable plugin marketplace (`novustech` /
`novus-skills`, versionless = every push is a new version). All shared content is now
**general and international** — no product brands, no country/persona/locale specifics,
no portfolio or session-history provenance, no personal benchmark products (see
`memory/project_content-policy.md`).

## Done this session

- Generalized + strengthened the five original skills (three sim verifications passed).
- Built `novus-design-system` (`skills/novus-design-system/`) from the design-system
  SharePoint kit with bundled `tokens.css` + Carlito snapshot (2026-07-07); full TDD
  incl. one closed loophole ("not SaaS" tagline).
- Made the repo a plugin marketplace (`.claude-plugin/`, `claude plugin validate` OK)
  and documented install + per-skill use cases in README §6.
- Dropped the AWS plugin from setup; `telegram` + `frontend-design` marked optional.
- Content scrub across README/PROMPTS/skills (commits `11ad7d5`…`a3295f3`): neutral
  intro, fictional §8 examples, internationalized personas, "fintech" removed,
  detailed five-slot goal prompt (§4 ②) with domain-generic benchmark guidance and
  worked banking/card examples.

## Verify

- [ ] End-to-end marketplace test from a fresh session:
      `/plugin marketplace add sgultom99/novus-skill` →
      `/plugin install novus-skills@novustech` → skills fire. Never run live.
- [ ] GitHub repo visibility is **private** (internal branding + SharePoint path
      inside `novus-design-system`).

## Open items

- **Credential rotation (urgent, user-side):** old transcripts under
  `~/.claude/projects/` still hold burned tokens — rotate.
- **`git push` blocked for Claude** by the permission classifier — Santo runs
  `! git push`; optionally add a Bash allow rule.
- **Design-system snapshot drift:** bundled tokens/fonts are the 2026-07-07 snapshot;
  refresh from the SharePoint master (owner: Rick) and re-date when the kit changes.

## Next session: start here

1. Run the end-to-end marketplace install test from a fresh session.
2. Confirm the GitHub repo is private.
3. Before publishing ANY doc/skill change, run the banned-terms grep in
   `memory/project_content-policy.md`.
4. When teammates onboard, collect friction points and iterate — fixes reach them via
   `/plugin marketplace update novustech`.
