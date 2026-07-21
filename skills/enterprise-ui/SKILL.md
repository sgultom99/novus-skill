---
name: enterprise-ui
description: Use when building or styling any user-facing UI — pages, components, consoles, dashboards, login screens, themes — for an enterprise or back-office product, before writing the first line of JSX/CSS/theme config. Also use when the user says a UI looks "fancy", "like an AI bot", "too rounded", or reports leftover template text or broken mobile layout.
---

# Enterprise UI (monochrome near-flat)

## Overview

Enterprise and back-office buyers expect **monochrome, near-flat, Ant Design-grade**
UI. Left to defaults, generated UIs come out with gradients, big radii, glow shadows,
and colored accents — the "AI-bot look" enterprise users reject on sight. Apply this
spec first; it is cheaper than the rework.

## The spec

| Property | Rule |
|---|---|
| Background | Solid neutral (`#fff` / `#f5f5f5` light, `#141414` dark). **Never a gradient.** |
| Border radius | ≤ 6px (Ant Design default). Buttons/inputs 4–6px, cards 6px. Never 8px+. |
| Elevation | 1px solid border (`#d9d9d9` / `#303030`) over box-shadow. If a shadow, subtle: `0 1px 2px rgba(0,0,0,.06)`. No glows. |
| Palette | Monochrome grays + ONE accent used sparingly (links, primary button). Default accent `#1677ff` (Ant) or the project's brand ink. No purple/teal/multi-color. |
| Typography | System stack or the project's existing font. Weights 400/600 only. No display fonts. |
| Components | If the project uses Ant Design, use AntD components and its theme tokens — do not hand-roll parallel widgets. |
| Motion | None beyond 0.15s color/border transitions. No entrance animations. |
| Density | Compact and information-dense. Enterprise back-office, not a landing page. |

## Drop-in tokens (framework-neutral starting point)

```css
:root {
  --bg: #f5f5f5;  --surface: #fff;  --border: #d9d9d9;
  --text: rgba(0, 0, 0, .88);  --muted: rgba(0, 0, 0, .45);
  --accent: #1677ff;  --radius: 6px;
}
.card { background: var(--surface); border: 1px solid var(--border);
        border-radius: var(--radius); }
```

(With Ant Design, set the same values via theme tokens — `colorPrimary`,
`borderRadius: 6` — instead of hand-rolled CSS.)

## Ship checklist (run before saying done)

1. **No gradient anywhere** — grep the diff for `gradient`.
2. **No leftover template text** — grep for CJK characters (`[一-鿿]`); scaffolds
   repeatedly leak Chinese strings. All user-facing text in the project's language(s),
   through its i18n system where one exists.
3. **Mobile pass** — check ~375px width: no horizontal scroll, menus that make no sense on
   mobile are hidden, touch targets ≥ 40px.
4. **No dead ends** — every link/menu item routes somewhere real; no "This page could not
   be found" behind a nav item you added.
5. **Radius audit** — nothing above 6px.
6. **Accessibility floor** — body text contrast ≥ 4.5:1 against its background, and a
   visible focus state on every interactive element (flat ≠ invisible).

## Common mistakes

| Generated instinct | Correction |
|---|---|
| Gradient hero/backdrop "for depth" | Solid neutral background |
| `border-radius: 12px` card + heavy shadow | 6px + 1px border |
| Brand-colored focus glow rings | 1px accent border on focus, at most a 2px pale ring |
| Decorative illustration/emoji in empty states | Plain text + one action button |
| "Trust-inspiring" navy/gold fintech theming | Monochrome; the product's data is the design |
