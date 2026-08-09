# M.M. Group — Design System (design.md)

AI-consumable design spec for the M.M. Group corporate website.
Source of truth: `css/style.css`. Verified against frontend-designer-skill v3.0 (May 2026).

## Tokens

```yaml
colors:
  primary:
    navy-900: oklch(0.253 0.067 271.2)
    navy:     oklch(0.316 0.109 279.5)
    blue:     oklch(0.474 0.124 255.2)
    cyan:     oklch(0.618 0.128 240.1)
    cyan-bright: oklch(0.692 0.131 234)
  text:
    ink:      oklch(0.219 0.043 261.6)
    slate:    oklch(0.524 0.046 258.8)
  bg:
    cloud:    oklch(0.981 0.005 258.3)
  # Semantic tokens use light-dark() for automatic dark mode.
  semantic:
    s-bg:           light-dark(cloud, oklch(0.168 0.03 271.2))
    s-surface:      light-dark(white, oklch(0.208 0.04 271.2))
    s-text:         light-dark(ink, oklch(0.94 0.014 240))
    s-text-muted:   light-dark(slate, oklch(0.72 0.03 245))
    s-border:       light-dark(oklch(0.92 0.008 260), oklch(0.31 0.04 271.2))
    s-primary:      blue
    s-accent:       cyan
typography:
  display: Lexend, 'Segoe UI', sans-serif
  body:    Source Sans 3, 'Segoe UI', sans-serif
  hero-h1: clamp(2.4rem, 1.3rem + 5.5vw, 4rem)
  section-h2: clamp(1.9rem, 1.2rem + 4cqi, 2.9rem)
spacing:
  base: 0.5rem    # 8px scale for macro layout
  micro: 0.25rem  # 4px scale for micro alignment
radius:
  sm: 8px
  card: 16px
  lg: 24px
  pill: 999px
shadows:
  sm: 0 1px 3px oklch(0.219 0.043 261.6 / 0.08)
  md: 0 6px 20px oklch(0.219 0.043 261.6 / 0.1)
  lg: 0 18px 44px oklch(0.219 0.043 261.6 / 0.14)
```

## Rules

- OKLCH only; HEX/HSL prohibited for new values. Alter via `oklch(from ...)`.
- Dark mode is automatic via `color-scheme: light dark` + `light-dark()` semantic tokens. Never hand-toggle a `.dark` class.
- Typography is fluid (`clamp`), container-driven via `cqi`, not viewport `vw`, for cards.
- Bento grids (`grid-template-areas`) for stats, companies, capabilities; uniform gap + radius.
- Glassmorphism is focalized: header, hero badge, about-exp badge. Low alpha + `backdrop-filter: blur(12–20px)` + 1px bright border.
- Aurora/mesh gradients are CSS-only stacked `radial-gradient`, `mix-blend-mode: screen`. No WebGL/Canvas.
- Focus: `:focus-visible` 2px + 3px offset, contrast > 3:1.
- Touch targets: buttons ≥ 44px min-height; nav links ≥ 24px (44px on mobile).
- Hover effects gated behind `@media (hover: hover) and (pointer: fine)`; `:active` for tactile.
- `prefers-reduced-motion: reduce` zeroes all animation.
- Below-fold sections use `content-visibility: auto` + `contain-intrinsic-size`.
- Images use `aspect-ratio` + `object-fit` (no padding-hack ratios).

## Layout Contract

| Section          | Desktop grid                         | Tablet (≤968)             | Mobile (≤576)        |
|------------------|--------------------------------------|---------------------------|----------------------|
| stats-grid       | 4 × 1fr                              | 2 × 1fr                   | 2 × 1fr              |
| companies-grid   | 12-col bento (7/5)                   | 1 col (stacked areas a,b) | 1 col                |
| caps-grid        | 12-col bento (8/4 + 4×3)             | 2 cols (2+2+4)            | 1 col                |
| gallery-grid     | 3 × 1fr                              | 2 × 1fr                   | 1 col                |
| footer-grid      | 1.6fr/1fr/1.1fr/1.5fr                | 2 × 1fr                   | 1 col                |
| content-grid     | 2 cols (reversible)                  | 1 col                     | 1 col                |
