---
name: relationship-dashboard design system
description: Core design tokens, component conventions, and palette for the relationship dashboard
type: project
---

## Palette (CSS custom props + PALETTE JS object)
- `--bg` / `bg`: `#0f0a14` (dark plum base)
- `--surface` / `surface`: `#1a1220`
- `--surface-2` / `surface2`: `#241829`
- `--rose` / `rose`: `#e8a5b8` (partner A accent)
- `--gold` / `gold`: `#d4af7a` (partner B accent)
- `--plum` / `plum`: `#9a6a8a`
- `--text` / `text`: `#f5ebe8`
- `--muted` / `muted`: `#9b8a92`
- `--border`: `rgba(245,235,232,0.08)`
- `grid`: `rgba(255,255,255,0.08)` (chart gridlines)

## Typography
- Headings: Fraunces serif (`.serif`, `.hero-italic`)
- Body: Inter
- `.label`: 12px, muted, uppercase, letter-spacing 0.08em

## Components
- `.card`: gradient bg (#241829→#1a1220), 16px radius, rose-tinted shadow, 1px border
- `.chip`: pill shape (999px radius), 12px text, partner-colored bg/border
- `.btn`: 12px radius, surface-2 bg, 44px min-height, 200ms ease transitions
- `.wcwrap`: word cloud canvas wrapper, 12px radius, near-transparent bg

## Conventions
- Micro-animations: 200ms ease
- Touch targets: 44px minimum enforced on `.btn`, `.input`
- Section headers: `.label` for "Section XX" kicker + `.serif text-2xl sm:text-3xl` for title
- Cards use `p-4` padding consistently
- Grid gaps: `gap-4`
