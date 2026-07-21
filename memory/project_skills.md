# Skills & marketplace

- Six skills in `skills/`, all TDD-verified (RED baseline → skill → GREEN sim):
  session-handover, skeptical-evaluator, enterprise-ui, secret-hygiene,
  production-handoff (all five general-purpose, brand-free by design decision
  2026-07-21) + novus-design-system (deliberately branded).
- A `merge-and-deploy` candidate was tested and DROPPED — its RED baseline passed
  (agents already run the full ship loop). Don't re-create it.
- Marketplace: `.claude-plugin/marketplace.json` (name `novustech`) + `plugin.json`
  (name `novus-skills`, repo root is the plugin, `skills/` auto-discovered). No
  version field on purpose: every git push = new version, no manual bumping.
  Install: `/plugin marketplace add sgultom99/novus-skill` →
  `/plugin install novus-skills@novustech`.
- novus-design-system sources: SharePoint site `novaAI1.0program` →
  `Shared Documents/Novus Design System` (owner: Rick). Access via
  `az account get-access-token --tenant 1c50ab90-92c9-452a-ab88-6a8f3f99557d`
  + Microsoft Graph (default az tenant fails: "Tenant does not have a SPO license").
  Bundled snapshot dated 2026-07-07 — refresh + re-date when the master changes.
- Claude cannot `git push` here (permission classifier); Santo runs `! git push`.
