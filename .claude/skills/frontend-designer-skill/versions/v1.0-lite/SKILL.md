---
name: frontend-designer-lite
description: >
  Concise framework-agnostic frontend design skill for UI/UX architecture.
  Covers CSS layers, container queries, OKLCH tokens, component patterns,
  WCAG 2.2 + EAA 2025, scroll-driven animations, Design.md, bento grids, performance.
  Trigger: When designing UI under time constraints — rapid component decisions,
  token systems, accessibility checks, or visual layout regardless of framework.
license: Apache-2.0
metadata:
  author: kozz36
  version: "1.0-lite"
---

## When to Use

- Rapid component design (card, modal, table, form, nav, hero, bento)
- Choosing CSS architecture (layers, nesting, tokens) under constraints
- Accessibility checks (contrast, focus, touch targets, reduced motion)
- Implementing scroll animations or micro-interactions
- Consuming a `Design.md` spec for deterministic AI generation
- Any visual layer decision where the framework is irrelevant

---

## 1. CSS Architecture 2026

### Cascade Layers

Specificity resolved by layer order, not selector weight. The browser handles conflict resolution internally.

```css
@layer vendor, tokens, reset, atoms, molecules, organisms, pages, utilities;
```

### Nesting & BEM

Native `&` replaces BEM. Combined with `@layer`, semantic class names suffice without verbose chains.

| Paradigm | Specificity | DOM Legibility | Verdict |
|----------|-------------|----------------|---------|
| BEM | String-dependent, error-prone | Poor | Obsolete |
| CSS Modules | Bundler hashes | Fair | Framework-coupled |
| Layers + Nesting | `@layer` deterministic | Excellent | **Standard** |

### :has()

Parent-level styling based on descendant state. No JS required for upward visual mutations. Universally supported since 2024.

```css
/* Form container reacts to invalid input */
.form-group:has(:invalid:not(:placeholder-shown)) {
  border-color: var(--s-border-error);
}

/* Card expands if containing open submenu */
.nav-card:has([aria-expanded="true"]) {
  grid-row: span 2;
}
```

### Paradigm Verdict

| Paradigm | Coupling | Performance | Verdict |
|----------|----------|-------------|---------|
| CSS-in-JS | Absolute | Poor (TBT) | **Prohibited** |
| Utility-First | High | High | **Desaconsejado** |
| Semantic CSS (Tokens) | Decoupled | Optimal | **Mandatory** |

---

## 2. Design Tokens (3-Tier)

1. **Primitive (`--p-`)**: Raw values. `--p-blue-500: oklch(0.6 0.15 250);`
2. **Semantic (`--s-`)**: Context roles via `light-dark()`. `--s-bg-surface: light-dark(var(--p-gray-50), var(--p-gray-900));`
3. **Component (`--c-`)**: Local override hooks. `--c-card-bg: var(--s-bg-surface);`

### OKLCH vs HEX/HSL

| Format | Gamut | Perceptual Uniformity | Manipulation |
|--------|-------|----------------------|--------------|
| HEX/RGB | sRGB | None | Unviable |
| HSL | sRGB | Poor | Low |
| **OKLCH** | Wide Gamut (P3/Rec2020) | Perfect | **Exceptional** |

**Verdict**: OKLCH mandatory. HEX/RGB/HSL prohibited for base specs.

Dynamic alterations use relative color functions without breaking contrast:

```css
--s-shadow-deep: oklch(from var(--p-surface-black) calc(l + 0.1) c h);
```

### Fluid Typography

Reject global viewport-based breakpoints. The component's own internal width governs its typography via `cqi` (container query inline), not the browser window.

```css
font-size: clamp(1rem, 0.8rem + 1.5cqi, 1.5rem);
```

Using `cqi` instead of `vw` ensures a card confined in a narrow sidebar correctly reduces its text size, even if the global viewport is extremely wide.

### Spacing

- 8-point multiples: macro layout, containers, section margins.
- 4-point multiples: micro-alignments, button padding.

### Dark Mode

`color-scheme: light dark;` at root. Semantic tokens declare dual values via `light-dark()`. No manual `.dark` class toggling.

---

## 3. Component Patterns

Five responsibilities per component: Layout, Skin, Typography, Animation, State.

| Component | Key Rule |
|-----------|----------|
| **Card** | `container-type: inline-size` mandatory. No external margins (parent grid imposes spacing). Images use strict `aspect-ratio` + `object-fit: cover`. |
| **Modal** | Use `<dialog>` + native `showModal()`. Backdrop via `::backdrop` with `backdrop-filter` or OKLCH darkening. `overscroll-behavior: contain` on body. |
| **Table** | `<table>` structure with `scope`. Complex layouts: `display: grid` + `subgrid` on rows. |
| **Form** | `:user-valid` / `:user-invalid` (post-interaction only). Error expansion: `grid-template-rows: 0fr` transition. |
| **Nav** | `<nav>` + lists. Sticky: `position: sticky`. Scroll animation: `animation-timeline: scroll()`. |
| **Hero** | `min-height: 100svh`. CSS mesh gradients only. |
| **Feature-Grid** | Bento: `repeat(12, 1fr)` + named `grid-template-areas`. Hero tiles span 4–6 cols; metrics span 2×1 or 2×2. |

**Philosophy**: Component-First replaces Mobile-First. Reconfigure via `@container`, not global viewport.

---

## 4. Responsive & Layout

- **Subgrid**: `grid-template-columns: subgrid` and `grid-template-rows: subgrid` are production-ready in all modern engines. Nested cards inherit parent tracks, aligning internal avatars, text, and buttons mathematically across adjacent cards. Solves uneven heights without JS equalization.
- **Named Areas**: Map topology directly in `grid-template-areas`. Prevents structural errors by AI agents and enables immediate human audits.
- **Aspect-Ratio**: `aspect-ratio: 16 / 9;` replaces padding-bottom hacks for all media containers.
- **Breakpoints**: Content-based, not device-based. Use `minmax()` + `ch` units (max 60–75ch reading width).
- **content-visibility**: `content-visibility: auto;` + `contain-intrinsic-size` defers below-the-fold layout and paint calculations until near viewport.

---

## 5. Accessibility (WCAG 2.2 + EAA 2025)

The European Accessibility Act (EAA 2025) is legally enforceable since June 2025. Penalties are fiscal. Align with EN 301 549, WCAG 2.1 AA, and selected WCAG 2.2 AAA criteria.

| Element | Requirement |
|---------|-------------|
| Standard text | Contrast ≥ 4.5:1 |
| Large text / UI borders | Contrast ≥ 3:1 |
| Focus indicator | 2px CSS thickness, ≥ 3:1 contrast against unfocused + adjacent |
| Touch target | **44×44 px** (exceeds WCAG 2.2 AA 24×24) |
| Reduced motion | `@media (prefers-reduced-motion: reduce)` — instant or fade-only |
| ARIA | Default to native HTML. `role` on `<div>` prohibited unless architect-managed. |

Focus indicator implementation:

```css
:focus-visible {
  outline: 2px solid var(--p-brand-base);
  outline-offset: 4px;
}
```

Floating headers must declare `scroll-padding-top` on the scroll container to prevent obscuring focused elements.

---

## 6. Micro-interactions & Animation

- Prefer CSS `transition` / `@keyframes` over WAAPI for predictable visual packaging.
- **Compositor-only properties**: `transform`, `opacity`. Prohibited during transitions: `width`, `height`, `margin`, `top`, `left`.
- **Scroll-driven**:
  - Root scroll: `animation-timeline: scroll();`
  - Component reveal: `animation-timeline: view();` + `animation-range: entry 0% entry 100%`
- **Hover without `:hover`**: Wrap in `@media (hover: hover) and (pointer: fine)`. Tactile feedback uses `:active`.

```css
@media (hover: hover) and (pointer: fine) {
  .btn:hover { transform: translateY(-2px); }
}
.btn:active { transform: scale(0.98); }
```

- **Skeletons**: Async states use `data-state="loading"`. Skeleton screens use animated `linear-gradient` via `background-position` shift:

```css
.skeleton {
  background: linear-gradient(90deg, var(--s-bg-surface) 25%, var(--s-text-muted) 50%, var(--s-bg-surface) 75%);
  background-size: 200% 100%;
  animation: skeleton-shift 1.5s infinite;
}
@keyframes skeleton-shift { to { background-position: -200% 0; } }
```

---

## 7. Visual Trends 2026

| Trend | Verdict |
|-------|---------|
| **Bento Grids CSS** | Mandatory for dashboards. Identical `gap` and `border-radius` (12–24px) across tiles. |
| **Glassmorphism** | Focalized only. Low-alpha + `backdrop-filter: blur(20px)` + 1px bright border. |
| **Neumorphism** | **Prohibited.** Violates EAA 2025 contrast/shape recognition. |
| **Bold Typography** | Standard. Variable font weight axes for hierarchy. |
| **Dark Mode First** | Standard. Design dark first; `light-dark()` lifts to light. |
| **Mesh/Aurora Gradients** | CSS-only. Stacked `radial-gradient` (OKLCH) + `mix-blend-mode`. No WebGL/Canvas. |

---

## 8. Performance Visual

| Property | Use | Impact |
|----------|-----|--------|
| `content-visibility` | Below-the-fold sections | Skips layout/paint until near viewport |
| `contain` | Component isolation | Prevents parent/sibling recalc on internal mutation |
| `will-change` | Pre-animation (transient) | Promotes to GPU layer. Remove after animation. |

**CLS Prevention**: `font-size-adjust` for async fonts. `aspect-ratio` for images. System UI fallback stack.

### Image Formats

| Format | Adoption (May 2026) | Verdict |
|--------|---------------------|---------|
| **WebP** | ~95.6% | Default for user-generated content. Fast encode (~90ms). |
| **AVIF** | ~93.8% browser / ~1.3% real delivery | Static assets only. Superior compression, slow encode (~1–2s). |
| **JPEG XL** | \<0.1% | Prohibited for direct clients. Insufficient WebKit/iOS support. |

**Variable Fonts**: Single file with weight/width/slant axes. Fewer requests, fluid interpolation for responsive hierarchy.

---

## 9. Design.md (Google Labs)

AI-consumable design spec replacing Figma handoff.

- **YAML Front Matter**: Machine-readable metadata. Declares design system structure:

```yaml
colors:
  primary: oklch(0.6 0.15 250)
  surface: light-dark(oklch(1 0 0), oklch(0.2 0.02 250))
typography:
  body: clamp(1rem, 0.8rem + 1cqi, 1.25rem)
spacing:
  base: 0.5rem  # 8px
radius:
  card: 16px
```

- **Markdown Prose**: Contextual rules for LLMs. *When* and *why* to invoke tokens.

**Toolchain**: Figma or Google Stitch (AI declarative tool) → auto-generate `design.md` semantic tree from canvases → Claude Code consumes the file via MCP (Model Context Protocol) → deterministic component generation without per-request contrast micromanagement.

---

## 10. Staff-Level Snippet

```css
@layer tokens, reset, layout, components;

@layer tokens {
  :root {
    --p-hue-brand: 250;
    --p-brand-base: oklch(0.60 0.15 var(--p-hue-brand));
    color-scheme: light dark;
    --s-bg-surface: light-dark(oklch(1 0 0), oklch(0.20 0.02 var(--p-hue-brand)));
    --s-text-primary: light-dark(oklch(0.15 0.02 var(--p-hue-brand)), oklch(0.99 0.01 var(--p-hue-brand)));
    --s-font-size-fluid: clamp(1rem, 0.8rem + 1cqi, 1.25rem);
  }
}

@layer layout {
  .bento {
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    gap: 1.5rem;
    grid-template-areas:
      "hero hero hero hero hero hero m1 m1 m1 m2 m2 m2"
      "hero hero hero hero hero hero ch ch ch ch al al";
  }
  .card { grid-column: span 12; }
  @media (min-width: 768px) { .card { grid-column: span 4; } .card--hero { grid-column: span 8; } }
}

@layer components {
  .card {
    container-type: inline-size;
    background: var(--s-bg-surface);
    border-radius: 16px;
    padding: 1.5rem;
    & :focus-visible { outline: 2px solid var(--p-brand-base); outline-offset: 4px; }
  }
  .card__title { font-size: var(--s-font-size-fluid); }
  @container (max-width: 320px) { .card__title { font-size: 1rem; } }
}
```

---

## 11. Architectural Dictates

**1. Absolute Structural Independence**  
No component declares external geometric coordinates. Final topology is imposed by the parent grid or self-referencing `@container` queries.

**2. Zero Global Device Viewports**  
`@media (min-width: ...)` is prohibited for internal component recompositions. All responsive transitions use `@container`, enforcing Component-First.

**3. OKLCH Only**  
HEX, RGB, and HSL are prohibited. Dynamic alterations use CSS relative color functions (`oklch(from var(--x) calc(l) c h)`).

**4. EAA 2025 Compliance**  
Focus rings: 2px CSS + >3:1 contrast. Touch targets: 44×44 px minimum.

**5. No Main-Thread Scroll Orchestration**  
`IntersectionObserver` and scroll listeners for decorative visuals are prohibited. Use `animation-timeline: view();` and `scroll()` for 60fps, zero TBT.

---

## Commands

```bash
# Contrast check (OKLCH-aware)
npx @adobe/spectrum-css-contrast-checker --token-file tokens.css

# Lighthouse accessibility + performance audit
npx lighthouse https://example.com --only-categories=accessibility,performance

# CSS specificity visualization
npx specificity-graph src/styles.css

# CSS support validation
npx browserslist "supports css-nesting and supports css-cascade-layers"

# AVIF encoding for static assets
avifenc --min 0 --max 63 --minalpha 0 --maxalpha 63 -a end-usage=q -a cq-level=18 -a tune=ssim input.png output.avif

# WCAG 2.2 compliance
npx axe-core-cli https://example.com --tags wcag22aa
```

---

## Resources

- CSS Nesting W3C: https://www.w3.org/TR/css-nesting-1/
- OKLCH — Evil Martians: https://evilmartians.com/chronicles/oklch-in-css-why-quit-rgb-hsl
- MDN Container Queries: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries
- CSS Scroll-Driven Animations: https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Scroll-driven_animations
- WCAG 2.2: https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/
- EAA 2025: https://www.chromatic.com/blog/developers-guide-to-european-accessibility-act-2025/
- Design.md: https://design.md
