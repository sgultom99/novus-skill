# Content policy for shared docs & skills (LOCKED by Santo, 2026-07-21)

Everything in this repo must read as general/international so any team can use it:

- No internal product brand names or internal repo names — describe generically
  ("the workflow project", "the KYC platform").
- No portfolio enumerations, project counts, or session-history provenance.
- No country/market specifics: no country names, locale lists, or localized persona
  names — use "<segment in your target market>" phrasing.
- No industry-niche keywords in persona/skill names (use `solution-architect`,
  not a niche-prefixed variant).
- No personal benchmark products — teach "benchmark a leader in YOUR domain" with
  generic banking/card worked examples instead.
- The company name "novustech" is allowed. Exception: `novus-design-system` is
  deliberately branded (its subject matter).

**Enforcement:** the concrete banned-terms grep (with the actual words) lives in the
maintainer's private Claude memory (`no-1nx-keyword`), NOT in this repo — putting the
list here would republish the very names it bans. Maintainer sessions load it
automatically; run it before every publish and require exit 1 (no matches).
