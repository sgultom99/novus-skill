---
name: novus-design-system
description: Use when building or styling any Novus Technologies frontend — website, app, dashboard, component, or HTML deck — or when the task mentions Novus branding or the nova* products/platform (novapay, novabank, novastore, novatrust, novaboost, novahub, novaware, novaserve, novaai) — before writing any HTML/CSS/JSX or choosing any color, font, or tagline.
---

# Novus Design System (frontend)

## Overview

The Novus design system is a finished artifact, not a direction. `tokens.css` is the
single source of truth: every color, type size, spacing, radius, shadow, the Carlito
font, and a full component library. **Never invent a value, component, or line of
brand copy — everything already exists; your job is to wire it in and derive from it.**

## Source the kit (in this order)

1. The project already contains `tokens.css` → use that copy.
2. This skill folder ships a snapshot (`tokens.css` + `fonts/*.woff2`, 2026-07-07) —
   copy both into the project, keeping `fonts/` beside `tokens.css`.
3. The living master: SharePoint site `novaAI1.0program` → `Shared Documents/Novus
   Design System/` (Graph API via `az` works). It also holds `Novus_Context.md`
   (voice, locked copy), `brand.html` (visual guide), `logos/` (all official assets),
   and the deck templates. Owner: Rick — flag drift to him, never patch one file alone.

## Wire it

- Link or import `tokens.css`; single-file/offline output instead **inlines it and
  embeds the woff2 as base64** (never `<link>` a stylesheet on a `file://` page).
- Use the shipped component classes — `.btn .btn--primary .card .card--interactive
  .card-trigger .badge .table .statrow .stat .bullets .modal .appbar .container
  .theme-nova*` — do not re-implement parallel buttons/cards/tables.

## Locked values

| Thing | Value |
|---|---|
| Typeface | Carlito via `var(--font-sans)` — no other font, no raw font-family |
| Page ground | White `--bg`; never a tinted page/panel ground |
| Accent | Novus Blue `#0070C0` = `--accent`/`--blue-500`; ONE accent per view; green is success/status only |
| Product accents (light) | novapay blue · novabank green `#00A04A` · novastore amber `#E8A300` · novatrust deep blue · novaboost orange `#E87830`; platform layers deep blue; novaai indigo `#534AB7` |
| Dark-mode lockups | ALL lockups drop identity color: pictograph + suffix `--blue-300`, "nova" `--blue-200` |
| Everything else | `var(--space-*)`, `var(--radius-*)`, `var(--text-*)`, `var(--shadow-*)` — no ad-hoc hex or px for anything the tokens define |

## Locked behaviors

1. **Dark mode = dual trigger.** Every dark rule under BOTH `[data-theme="dark"]` and
   `@media (prefers-color-scheme: dark)`; ship a persisted moon/sun toggle, applied
   before paint. OS-only or toggle-only is a defect.
2. **Logos are placed assets.** Master lockup via `--logo-master-*` (colour in light,
   white in dark), products/platform via `--logo-product-*`/`--logo-platform-*` from
   `logos/`. A typed or CSS-drawn "NOVUS"/monogram is not the logo. Product names are
   lowercase and solid-set — `novapay`, never `nova pay` or `NovaPay`; standalone
   placement uses the full lockup (pictograph tight to the wordmark).
3. **One hover treatment, only on clickables.** Clickable boxes: `translateY(-4px)` +
   raised shadow + accent border, whole card clickable via a `.card-trigger`
   stretched-link. Non-clickable tiles (logo walls, info/stat tiles) get NO hover.
4. **Mobile-first.** Base CSS targets the phone, scale up with `min-width` queries;
   375px is the test baseline; touch targets ≥44px; no hover-only interactions;
   prefer native open/close (`<details>`, `<dialog>`) so the UI works with JS off.
5. **App shell.** One centered content column `--container` (1200px) shared by header,
   nav, and main; master lockup in the header at `--logo-height` (26px).
6. **Copy is sourced, not written.** Bullets use the hanging-indent `›` `.bullets`
   component. The business model is **"Service as Software"**: the strings "SaaS" and
   "Software as a Service" must not appear in output at all — a negated or contrast
   use ("not SaaS", "unlike SaaS") is still an occurrence. Expand the model positively:
   Novus provides the technology AND runs the operations. Taglines, positioning lines,
   and figures come from `Novus_Context.md` / the master decks; if you don't have it,
   leave a `<!-- copy: Novus_Context.md -->` slot rather than inventing one.

## Ship checklist

1. Grep the diff for hex/px literals the tokens define → replace with `var(--…)`.
2. Dark parity: check with the toggle AND with OS-dark + JS off — logo swaps to
   white, lockups go light-blue, no dark-on-dark text.
3. 375px pass: no horizontal scroll, ≥44px targets, menus/modals work with JS off.
4. Hover audit: every hover box does something on click; static tiles don't react.
5. `grep -ri "saas\|software as a service"` on output copy → zero matches, including
   inside taglines and negations ("not SaaS" is a match, not a pass).

## Common mistakes

| Generated instinct | Correction |
|---|---|
| Invent a "fitting" palette/font (navy+gold, Inter/IBM Plex) | Carlito + the token palette, wired from `tokens.css` |
| CSS-drawn monogram or typed wordmark as the logo | Place the official asset; swap colour→white in dark |
| `prefers-color-scheme` only (or toggle only) | Both triggers, every rule mirrored |
| Re-implement buttons/cards/badges from scratch | The shipped component classes |
| Write a fresh tagline / product blurb | Pull from `Novus_Context.md`; leave a sourced-copy slot if absent |
| Product accent colors kept in dark mode | Dark lockups are always the two light blues |
