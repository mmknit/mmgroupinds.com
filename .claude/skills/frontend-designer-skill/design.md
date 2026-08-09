---
# Design System Specification
# Framework-Agnostic Design Tokens — Local design.md Handoff Pattern
# Generated for: frontend-designer-skill demonstration
# Reference: local project artifact; not an external standard

name: frontend-designer-demo
version: "1.0"
author: kozz36
license: Apache-2.0
---

# Design System: Framework-Agnostic Tokens

> This file demonstrates a local `design.md` handoff pattern consumed by AI agents (Claude Code, Cursor, Copilot) for deterministic visual layer generation.
> Agents may read this file when triggered by design-token, component-pattern, or accessibility contexts.

---

## Front Matter (YAML)

```yaml
colors:
  primary:
    base: oklch(0.60 0.15 250)
    light: oklch(0.95 0.05 250)
    dark: oklch(0.25 0.10 250)
  surface:
    canvas: light-dark(oklch(0.99 0.01 250), oklch(0.15 0.02 250))
    elevated: light-dark(oklch(1 0 0), oklch(0.20 0.02 250))
  text:
    primary: light-dark(oklch(0.15 0.02 250), oklch(0.99 0.01 250))
    muted: light-dark(oklch(0.4 0.05 250), oklch(0.7 0.05 250))
    inverse: light-dark(oklch(0.99 0.01 250), oklch(0.15 0.02 250))
  semantic:
    success: oklch(0.7 0.2 146)
    warning: oklch(0.7 0.15 85)
    error: oklch(0.6 0.2 25)
    info: oklch(0.7 0.1 250)

typography:
  family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif
  scale:
    display: clamp(2.5rem, 1.5rem + 4cqi, 4rem)
    h1: clamp(2rem, 1.2rem + 3cqi, 3rem)
    h2: clamp(1.5rem, 1rem + 2cqi, 2.25rem)
    h3: clamp(1.25rem, 0.9rem + 1.5cqi, 1.75rem)
    body: clamp(1rem, 0.8rem + 1cqi, 1.25rem)
    small: clamp(0.875rem, 0.75rem + 0.5cqi, 1rem)
  line-height:
    tight: 1.2
    normal: 1.5
    relaxed: 1.75
  weight:
    regular: 400
    medium: 500
    semibold: 600
    bold: 700

spacing:
  base: 0.5rem      # 8px
  scale:
    xs: 0.25rem      # 4px
    sm: 0.5rem       # 8px
    md: 1rem         # 16px
    lg: 1.5rem       # 24px
    xl: 2rem         # 32px
    2xl: 3rem        # 48px

radius:
  sm: 8px
  md: 12px
  lg: 16px
  xl: 24px
  full: 9999px

shadow:
  sm: "0 1px 2px oklch(0.15 0.02 250 / 0.05)"
  md: "0 4px 6px oklch(0.15 0.02 250 / 0.07), 0 1px 3px oklch(0.15 0.02 250 / 0.05)"
  lg: "0 10px 15px oklch(0.15 0.02 250 / 0.1), 0 4px 6px oklch(0.15 0.02 250 / 0.05)"
  xl: "0 20px 25px oklch(0.15 0.02 250 / 0.12), 0 10px 10px oklch(0.15 0.02 250 / 0.04)"

animation:
  duration:
    fast: 150ms
    normal: 250ms
    slow: 350ms
  easing:
    default: cubic-bezier(0.4, 0, 0.2, 1)
    enter: cubic-bezier(0, 0, 0.2, 1)
    exit: cubic-bezier(0.4, 0, 1, 1)
    bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55)
```

---

## Narrative Rules (Markdown)

### Color Usage

**Rule 1: Primary palette is reserved for interactive elements and brand identity.**
The `colors.primary.base` token (oklch(0.60 0.15 250)) is exclusively applied to:
- Buttons with user-initiated actions (submit, confirm, proceed)
- Active navigation indicators
- Progress bars and loading spinners
- Brand logo or favicon

Do NOT apply primary color to:
- Static text headers (use `text.primary`)
- Background surfaces (use `surface.canvas` or `surface.elevated`)
- Disabled states (use `text.muted`)

**Rule 2: Semantic colors carry behavioral meaning.**
- `semantic.success` → confirmation, completion, positive metrics
- `semantic.warning` → pending, requires attention, non-blocking issues
- `semantic.error` → blocking failures, validation errors, critical alerts
- `semantic.info` → neutral context, tips, informational banners

The destructive error palette is implemented on modals and notifications only; never on passive interactive elements like tabs or toggles.

### Typography Scale

**Rule 3: Fluid typography responds to container width, not viewport.**
All `typography.scale.*` values use `cqi` (container query inline) units. This ensures a card confined in a narrow sidebar correctly reduces its text size even if the global viewport is extremely wide.

**Rule 4: Line-height correlates with content density.**
- Single-line elements (buttons, badges): `line-height.tight` (1.2)
- Body paragraphs: `line-height.normal` (1.5)
- Long-form reading (articles, documentation): `line-height.relaxed` (1.75)

### Spacing Grid

**Rule 5: The 8-point grid is non-negotiable for macro structure.**
- All section margins, card gaps, and layout gutters must be multiples of `spacing.base` (0.5rem / 8px).
- Internal component padding may use 4px increments (`spacing.xs`) for micro-alignments (button icon + text pairs, form label + input proximity).
- Never use arbitrary values like 7px, 13px, or 23px. Round to nearest 4px or 8px.

### Component Tokens

**Rule 6: Component tokens are override hooks, not defaults.**
Every component MUST declare its visual properties using `var(--c-component-property, var(--s-semantic-token))` pattern. This enables:
- Theme override without component modification
- Per-instance customization via inline style overrides
- Dark mode inheritance through semantic tokens

Example:
```css
.card {
  --c-card-bg: var(--s-surface-elevated);
  --c-card-radius: var(--radius-md);
  background-color: var(--c-card-bg);
  border-radius: var(--c-card-radius);
}
```

### Accessibility Constraints

**Rule 7: Contrast ratios are mathematically enforced.**
- Standard text (≥16px regular): minimum 4.5:1 against background
- Large text (≥24px regular / ≥18px bold): minimum 3:1 against background
- UI borders and focus rings: minimum 3:1 against adjacent colors

Because OKLCH lightness (L) is perceptually uniform, adjusting the C or H channels will NOT alter the contrast ratio. This enables safe dynamic theming without re-calculating contrast.

**Rule 8: Focus indicators are mandatory and styled via `outline`.**
All interactive elements MUST declare:
```css
:focus-visible {
  outline: 2px solid var(--colors-primary-base);
  outline-offset: 4px;
}
```
Prohibited: `outline: 1px dotted`, faint shadows, or color-only focus indicators.

**Rule 9: Touch targets exceed regulatory minimums.**
- Primary buttons: minimum 44×44 px (exceeds WCAG 2.2 AA 24×24 px)
- Secondary/icon-only buttons: minimum 40×40 px
- Links inline with text: minimum 24×24 px with 8px padding expansion on tap

**Rule 10: Motion respects user preference.**
All animations MUST respect `@media (prefers-reduced-motion: reduce)`:
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

### Layout Patterns

**Rule 11: Component-First replaces Mobile-First.**
Reconfigurations respond to `@container` queries, not global `@media` breakpoints. A component's own internal width governs its layout mutations.

**Rule 12: Bento grids for dashboards.**
Use `grid-template-columns: repeat(12, 1fr)` with named `grid-template-areas`. Hero tiles span 4–6 columns; metrics span 2×1 or 2×2. Maintain identical `gap` and identical `border-radius` across all tiles for puzzle-fit rhythm.

**Rule 13: Aspect-ratio replaces padding hacks.**
All media containers (images, video, skeleton placeholders) MUST use `aspect-ratio: 16 / 9` or `aspect-ratio: 1 / 1`. Never use the legacy `padding-bottom` percentage trick.

### Performance

**Rule 14: Content-visibility for below-the-fold sections.**
Sections outside the initial viewport MUST declare:
```css
.section-below-fold {
  content-visibility: auto;
  contain-intrinsic-size: auto 300px;
}
```

**Rule 15: Image formats prioritized by use case.**
- User-generated content (thumbnails, avatars): WebP (~95.6% browser support, fast encode)
- Static UI assets (logos, icons): AVIF (~93.8% support, superior compression)
- Client-facing direct delivery: JPEG XL prohibited due to insufficient WebKit/iOS support

---

## Token Consumption Contract

When an AI agent consumes this `design.md` file via MCP, it MUST:

1. **Suspend external color guessing** — never generate Tailwind default blues or generic grays. Extract the exact OKLCH values from the YAML front matter.
2. **Respect the 8-point spacing grid** — all margins, paddings, and gaps MUST be multiples of 8px (or 4px for micro-alignments).
3. **Use `light-dark()` for theming** — never add `.dark` class toggling. All semantic tokens already encode dual values.
4. **Apply `cqi` for fluid typography** — never use `vw` or fixed breakpoints for text scaling inside components.
5. **Validate contrast before emitting CSS** — ensure any generated color pair meets the minimum ratios declared in Rule 7.
6. **Preserve component token hooks** — emit CSS custom properties with `--c-` prefix as override points for downstream developers.

---

## Verification Checklist

| Claim | Source | Status |
|-------|--------|--------|
| OKLCH perceptually uniform | Evil Martians: https://evilmartians.com/chronicles/oklch-in-css-why-quit-rgb-hsl | ✅ Verified |
| `light-dark()` native support | MDN: https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark | ✅ Verified |
| Container queries (`cqi`) production | MDN: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries | ✅ Verified |
| EAA 2025 enforceable June 2025 | Chromatic: https://www.chromatic.com/blog/developers-guide-to-european-accessibility-act-2025/ | ✅ Verified |
| WCAG 2.2 focus: 2px minimum | W3C: https://www.w3.org/WAI/WCAG22/Understanding/focus-appearance.html | ✅ Verified |
| `content-visibility` performance | MDN: https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility | ✅ Verified |
| WebP adoption ~95.6% | Can I Use: https://caniuse.com/webp | ✅ Verified |
| AVIF adoption ~93.8% | Can I Use: https://caniuse.com/avif | ✅ Verified |

---

*This design.md is maintained as part of the frontend-designer-skill repository. Update alongside SKILL.md version bumps.*
