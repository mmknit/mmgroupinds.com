---
name: frontend-designer
description: >
  Framework-agnostic frontend design skill for UI/UX architecture.
  Covers CSS layers, container queries, OKLCH design tokens, component patterns,
  WCAG 2.2 + EAA 2025 accessibility, scroll-driven animations, visual trends 2026,
  and Design.md (Google Labs standard for AI design-to-code).
  Trigger: When designing or implementing UI components, CSS architecture,
  design systems, responsive layouts, visual accessibility, or design tokens —
  regardless of React, Vue, or HTML.
license: Apache-2.0
metadata:
  author: kozz36
  version: "1.0"
---

## When to Use

- Designing or implementing UI components (cards, modals, tables, forms, nav, hero, feature-grid)
- Architecting CSS systems with cascade layers, container queries, or semantic tokens
- Building design systems with primitive → semantic → component token tiers
- Ensuring WCAG 2.2 + EAA 2025 compliance (contrast, focus, touch targets, reduced motion)
- Implementing scroll-driven animations, micro-interactions, or skeleton screens
- Deciding on visual trends (bento grids, glassmorphism, mesh gradients, dark mode first)
- Consuming or authoring a `Design.md` spec (Google Labs AI design-to-code standard)
- Any visual layer decision where the framework (React/Vue/HTML) is irrelevant

---

## 1. CSS Architecture 2026

### Cascade Layers (@layer)

Specificity is controlled unambiguously by `@layer`. The browser resolves conflicts by layer order, not selector weight or import sequence.

**Mandatory layer sequence** (lowest → highest priority):

```css
@layer vendor, tokens, reset, atoms, molecules, organisms, pages, utilities;
```

A utility class always overrides a complex component. Third-party styles (vendor) cannot collide with internal design system decisions.

### Native Nesting & BEM Obsolescence

The `&` selector references the parent context natively. Combined with `@layer`, it eliminates the need for BEM's verbose class chains.

| Paradigm | Specificity Control | DOM Legibility | Maintainability |
|----------|---------------------|----------------|-----------------|
| **BEM (2020)** | Depends on long strings (`.card__title--active`). Error-prone at scale. | Poor. HTML polluted with redundant classes. | High, but requires strict manual discipline. |
| **CSS Modules / Scoped (2023)** | Isolation via bundler-generated hashes. | Fair. Obfuscated classes hinder browser DevTools debugging. | Moderate. Tightly coupled to JS bundler pipeline. |
| **Layers + Nesting (2026)** | Deterministic `@layer` resolution. Browser handles conflict, not selector length. | Excellent. Semantic HTML with concise classes (`.card`, `.title`). | Exceptional. Safe modification without preprocessors or bundlers. |

⚠️ **Anti-pattern: BEM in new codebases**
Do NOT use BEM naming in greenfield projects. `@layer` + native nesting provide the same specificity control without polluting the DOM. BEM is acceptable only in legacy codebases where a full migration to `@layer` is not feasible.

### :has() — Relational Selector

The `:has()` pseudo-class applies styles to a parent based on descendant state. It removes JS for upward visual mutations.

```css
/* Form container reacts to any invalid input */
.form-group:has(:invalid:not(:placeholder-shown)) {
  border-color: var(--s-border-error);
  box-shadow: 0 0 0 4px var(--s-shadow-error);
}
```

⚠️ **Anti-pattern: :has() for layout decisions**
Do NOT use `:has()` to decide macro layout (e.g., `.sidebar:has(.active)` to change grid areas). `:has()` is for visual state reactions on micro-interactions. Macro layout must be governed by `@container` or parent grid.

### Paradigm Verdict

| Paradigm | Visual/Structural Coupling | Parse Performance | Framework-Agnostic Verdict |
|----------|--------------------------|-------------------|---------------------------|
| **CSS-in-JS (Styled Components)** | Absolute coupling. Style lives in JS thread only. | Poor. Blocks main thread, increases TBT. | **Prohibited.** Incompatible with static rendering and agnostic architecture. |
| **Utility-First (Tailwind v4.2)** | High. DOM is the sole visual source of truth. Requires mapping hundreds of classes per component. | High. Static CSS pre-generated via native compilation. | **Desaconsejado.** Transferring a design to a frontend architect forces token visual noise into component logic. |
| **Semantic CSS (Token System)** | Total decoupling. HTML describes content (`<article class="card">`); CSS governs display. | Optimal. Static CSS files, CDN-cacheable, parallelizable. | **Mandatory Standard.** Enables AI agents to generate stable interfaces that JS developers consume without friction. |

**Decision Tree: Which CSS paradigm?**

```
if component is reusable across projects → Semantic CSS (Token System)
if component is one-off and time-critical → Utility-First (acceptable tradeoff)
if runtime JS bundle size is constrained → NEVER CSS-in-JS
if SSR or SSG is required → NEVER CSS-in-JS
if static build with zero JS dependency is required → Semantic CSS only
```

---

## 2. Design Tokens (3-Tier System)

Tokenization uses a relational semantic graph. Theme reassignment happens without component-level restructuring.

### Three-Tier Hierarchy

1. **Primitive (Tier 1 — `--p-`)**: Absolute, mathematical, raw values. Color palettes, spatial scales, base font families. Agnostic to context and theme. Never mutate in dark mode.  
   Example: `--p-blue-500: oklch(0.6 0.15 250);`

2. **Semantic (Tier 2 — `--s-`)**: Assigns intent and purpose. Roles: surface backgrounds, error text, interactive borders, visual accents. Inherently dynamic. Uses `light-dark()` to alternate primitive values based on active color scheme.  
   Example: `--s-bg-surface: light-dark(var(--p-gray-50), var(--p-gray-900));`

3. **Component (Tier 3 — `--c-`)**: Encapsulated override hooks within a component block. Consume semantic tokens as fallbacks, but allow local injection for specific variations.  
   Example: `background-color: var(--c-card-bg, var(--s-bg-surface));`

### Colorimetry: OKLCH vs HEX/HSL

| Format | Gamut | Perceptual Uniformity | Mathematical Manipulation |
|--------|-------|----------------------|---------------------------|
| **HEX / RGB** | sRGB only. | None. No correlation between numbers and luminosity perception. | Unviable. Cannot darken or saturate algorithmically in CSS. |
| **HSL** | sRGB only. | Poor. Same L value can appear drastically different, breaking contrast calculations. | Low. Hue shifts alter perceived brightness unpredictably. |
| **OKLCH** | Display-P3 and Rec2020 (Wide Gamut). ~30% more vibrant. | Perfect. Lightness is constant regardless of Hue and Chroma. | Exceptional. CSS relative colors (`oklch(from base l c h)`) preserve perceptual contrast. |

**Verdict**: OKLCH is mandatory. HEX, RGB, and HSL are prohibited for base specifications.

⚠️ **Anti-pattern: OKLCH for legacy system integration**
Do NOT retroactively convert an entire legacy codebase from HEX to OKLCH in one PR. The migration must be incremental: new components use OKLCH; existing components are migrated when touched. Maintain a `legacy-colors.css` shim if needed.

**Decision Tree: Which token tier to use?**

```
if defining raw color / space / font-family → Tier 1 (Primitive: --p-)
if assigning meaning to a primitive (background, text, border) → Tier 2 (Semantic: --s-)
if component needs local override without affecting system → Tier 3 (Component: --c-)
if the value never changes between light/dark modes → Tier 1 (Primitive)
if the value must flip between light/dark modes → Tier 2 (Semantic) using light-dark()
```

### Fluid Typography

Reject viewport-based breakpoints. Use `clamp()` with container query inline units (`cqi`).

```css
font-size: clamp(1rem, 0.8rem + 1.5cqi, 1.5rem);
```

Using `cqi` instead of `vw` ensures a component (e.g., a card) reduces its typography appropriately when confined in a narrow column, even if the global browser viewport is extremely wide.

⚠️ **Anti-pattern: vw for component-internal typography**
Do NOT use `vw` for text inside components. `1vw` on a 4K monitor produces unreadably large text inside a narrow sidebar card. Always use `cqi` for component-internal fluid typography.

### Spacing Grid

- **8-point multiples**: macro layout, containers, section margins.
- **4-point multiples**: micro-alignments, button padding, icon gaps.

Injected via primitive tokens:

```css
--p-space-8: 0.5rem;
--p-space-16: 1rem;
--p-space-24: 1.5rem;
```

⚠️ **Anti-pattern: Arbitrary spacing values**
Do NOT use values like `7px`, `13px`, or `23px`. These desynchronize the vertical rhythm and cause sub-pixel blur on low-DPI screens. Round to the nearest 4px or 8px multiple.

### Dark Mode First

Use `color-scheme: light dark;` at root. All semantic tokens declare dual values via `light-dark()`. Component tokens inherit this automatically. No manual `.dark` class toggling.

---

## 3. Component Patterns

Every component decomposes into **five structural responsibilities**:

1. **Layout**: Internal geometry (Flexbox / CSS Grid).
2. **Skin**: Semantic tokens for backgrounds, borders, shadows.
3. **Typography**: Font families, weights, line-height, fluid scales.
4. **Animation**: Transition orchestration without layout reflow properties.
5. **State**: Interactive visual representation (hover, focus-visible, invalid, disabled).

### Component Rules

| Component | Key Rule |
|-----------|----------|
| **Card** | `container-type: inline-size` mandatory. No external margins. Images use strict `aspect-ratio` + `object-fit: cover`. |
| **Modal** | Use `\u003cdialog\u003e` + `showModal()`. Backdrop via `::backdrop`. `overscroll-behavior: contain`. |
| **Table** | `\u003ctable\u003e` structure with `scope`. Complex layouts: `display: grid` + `subgrid` on rows. |
| **Form** | `:user-valid` / `:user-invalid` (post-interaction only). Error expansion: `grid-template-rows: 0fr` transition. |
| **Nav** | `\u003cnav\u003e` + lists. Sticky: `position: sticky`. Scroll animation: `animation-timeline: scroll()`. |
| **Hero** | `min-height: 100svh`. CSS mesh gradients only. |
| **Feature-Grid** | Bento: `repeat(12, 1fr)` + named `grid-template-areas`. Hero tiles span 4–6 cols; metrics span 2×1 or 2×2. |

⚠️ **Anti-pattern: External margins on components**
Do NOT declare `margin` on component root elements (e.g., `.card { margin: 16px; }`). Margins are a **parent responsibility** imposed by the macro layout grid (`gap`). Components must be spatially ignorant — they adapt to whatever container they are placed in.

⚠️ **Anti-pattern: Missing container-type on cards**
Do NOT omit `container-type: inline-size` on card components. Without it, internal `@container` queries fail silently, and fluid typography inside the card collapses to global viewport dimensions instead of card dimensions.

### Philosophy Shift

**Mobile-First and Desktop-First are obsolete.** Replaced by **Component-First**: spatial reconfigurations respond to the component's own internal content stress via `@container`, not the global device viewport.

**Decision Tree: Which layout primitive?**

```
if one-dimensional flow (row or column) → Flexbox
if two-dimensional grid (rows + columns) → CSS Grid
if nested component must inherit parent tracks → Subgrid
if component must adapt its own internals → @container
if macro page layout (header, sidebar, main, footer) → CSS Grid + @media (acceptable exception)
if aligning items of unknown/dynamic size → Flexbox
```

---

## 4. Responsive & Layout

### Subgrid

`grid-template-columns: subgrid` and `grid-template-rows: subgrid` are production-ready. A nested card inside a 12-column macro-layout can inherit the parent's tracks, aligning internal avatars, text, and buttons mathematically across adjacent cards. Solves the historic "uneven content height" problem without JS equalization.

### Named Areas

Map visual topology directly in CSS:

```css
.bento-layout {
  display: grid;
  grid-template-columns: repeat(12, 1fr);
  grid-template-areas:
    "hero hero hero hero hero hero metric1 metric1 metric1 metric2 metric2 metric2"
    "hero hero hero hero hero hero chart chart chart chart alert alert";
}
```

This prevents structural errors by AI agents and enables immediate human code audits.

### Aspect-Ratio & Content-Visibility

- `aspect-ratio: 16 / 9;` replaces padding-bottom hacks for all media containers.
- Breakpoints must be content-based, not device-based. Use `minmax()` and `ch` units to enforce optimal reading width (60–75ch max).
- `content-visibility: auto;` with `contain-intrinsic-size` defers layout and paint calculations for below-the-fold sections.

⚠️ **Anti-pattern: Device-based breakpoints for component internals**
Do NOT use `@media (min-width: 768px)` to change the internal layout of a card, modal, or form component. `@media` is acceptable only for macro page structure (sidebar collapse, grid reconfiguration at page level). Component internals MUST use `@container`.

**Decision Tree: When is @media acceptable?**

```
if adjusting macro page layout (sidebar, main area, footer grid) → @media acceptable
if adjusting internal component layout (card columns, modal width, form fields) → @container mandatory
if the change affects typography inside a component → @container with cqi
if the change is purely decorative (padding on ultra-wide) → @media acceptable
```

---

## 5. Accessibility (WCAG 2.2 + EAA 2025)

The European Accessibility Act (EAA 2025) is legally enforceable since June 2025. Penalties are fiscal. Align with EN 301 549, WCAG 2.1 AA, and selected WCAG 2.2 AAA criteria.

### Contrast Ratios

| Element | Minimum Ratio |
|---------|--------------|
| Standard text | **4.5:1** |
| Large text (24px regular / 18px bold) | **3:1** |
| UI components (input outlines, interactive borders) | **3:1** |

OKLCH enables mathematical contrast resolution at design time because Lightness is independent of Hue and Chroma.

### Focus Indicators

WCAG 2.2 criteria 2.4.11 and 2.4.13 dictate: minimum 2px CSS border perimeter; color ≥ 3:1 against unfocused state AND adjacent background; focused element must be fully visible (`scroll-padding-top` on scroll containers).

```css
:focus-visible {
  outline: 2px solid var(--p-brand-base);
  outline-offset: 4px;
}
```

Prohibited: `outline: 1px dotted`, faint blurred shadows.

⚠️ **Anti-pattern: Color-only focus indicators**
Do NOT rely solely on `box-shadow` or `background-color` changes for focus. Users with low vision or color blindness may miss them. The 2px solid outline with offset is the only guaranteed perceptible indicator.

### Touch Targets & Reduced Motion

- **Touch target**: 44×44 px minimum for primary buttons (exceeds WCAG 2.2 AA 24×24 px).
- `@media (prefers-reduced-motion: reduce)` is mandatory. Neutralize scale/parallax animations instantly or fade only.

⚠️ **Anti-pattern: Static reduced-motion disable**
Do NOT simply set `animation: none` under `prefers-reduced-motion`. This can break layout if elements rely on animated visibility. Use instant transitions (0ms) or opacity fades only.

### ARIA Rule

> "The best ARIA practice is to use native HTML elements instead of ARIA whenever possible."

Default to native interactive elements: `<button>`, `<dialog>`, `<nav>`, `<fieldset>`, `<details>`. `role="button"` on `<div>` elements is prohibited unless strictly managed by the frontend architect for asynchronous semantic control.

⚠️ **Anti-pattern: ARIA on static elements**
Do NOT add `role="presentation"` or `aria-hidden="true"` to focusable elements. This removes them from the accessibility tree while keeping them in the tab order, creating keyboard traps.

**Decision Tree: Do I need ARIA?**

```
if native HTML element exists for the purpose → Use native element; NO ARIA needed
if custom widget requires keyboard interaction → Add ARIA roles + keyboard handlers
if hiding decorative element from screen readers → aria-hidden="true" (only if unfocusable)
if element is visible but not interactive → No role needed
```

---

## 6. Micro-interactions & Animation

### CSS Transitions vs WAAPI

Prefer declarative `transition` and `@keyframes`. WAAPI is reserved for imperative orchestration controlled by JS logic; CSS declarations guarantee visual state remains packaged regardless of React or Vanilla JS injection.

### Compositor-Only Properties

Apply animations exclusively to `transform` and `opacity`. **Strictly prohibited during state transitions**: `width`, `height`, `margin`, `top`, `left` — these trigger Layout Reflow and Paint, destroying 60fps consistency.

⚠️ **Anti-pattern: Animate layout-triggering properties**
Do NOT animate `width`, `height`, `margin`, `top`, or `left` during state transitions. If a card must expand, animate `transform: scale()` or use `grid-template-rows: 0fr → 1fr` for height expansion without reflow.

### Scroll-Driven Animations

| Context | Property | Architectural Use |
|---------|----------|-------------------|
| **Root scroll** | `animation-timeline: scroll();` | Shrink sticky headers, global reading progress bar (0% → 100%). |
| **Component intersection** | `animation-timeline: view();` | Staggered card reveals as they enter viewport. Combine with `animation-range: entry 0% entry 100%`. |

⚠️ **Anti-pattern: IntersectionObserver for decorative visuals**
Do NOT use `IntersectionObserver` or scroll event listeners for purely decorative visual effects (parallax, fade-in reveals). These execute on the main thread and increase TBT. Use `animation-timeline: view()` instead.

### Hover Without :hover

Wrap hover styles inside `@media (hover: hover) and (pointer: fine)` to prevent "sticky hover" on touch devices. Tactile feedback uses `:active` exclusively.

```css
@media (hover: hover) and (pointer: fine) {
  .btn-primary:hover {
    background-color: oklch(from var(--p-brand-base) calc(l + 0.05) c h);
  }
}
```

⚠️ **Anti-pattern: :hover without media query guard**
Do NOT declare `.btn:hover` without `@media (hover: hover)` guard. On touch devices, the hover state "sticks" after tap, leaving buttons visually highlighted incorrectly.

### Skeleton Screens & State Attributes

Async states use `data-state="loading"`. Skeleton screens use animated `linear-gradient` via `background-position` shift, consuming the base background token and a contrast token.

⚠️ **Anti-pattern: Skeleton with random shimmer colors**
Do NOT use arbitrary gray colors for skeleton shimmer. Derive the shimmer gradient from the design tokens: base background → muted text color → base background. This ensures skeletons do not violate dark mode.

**Decision Tree: Which animation strategy?**

```
if triggered by user interaction (click, hover) → CSS transition on transform/opacity
if triggered by scroll position (reveal, parallax) → animation-timeline: scroll() / view()
if complex orchestration with dynamic values → WAAPI (JS-controlled, not CSS)
if element enters/exits DOM (mount/unmount) → transition + opacity + transform
if loading state placeholder → Skeleton with linear-gradient animation (CSS only)
```

---

## 7. Visual Trends 2026 (Opinionated)

| Trend | Verdict | Implementation |
|-------|---------|---------------|
| **Bento Grids CSS** | **Mandatory** for dashboards/metrics. Asymmetric tile sizing signals data density. Requires identical `gap` and identical `border-radius` (12–24px) across all tiles for puzzle-fit rhythm. |
| **Glassmorphism** | **Focalized only.** Overlays, sticky navbars, contextual menus. Use low-alpha transparency + `backdrop-filter: blur(20px)` + 1px bright semi-transparent border + layered `box-shadow`. |
| **Neumorphism** | **Prohibited.** Extruded monochrome interfaces with dual opposing shadows violate EAA 2025 contrast thresholds for shape recognition by visually impaired users. |
| **Bold Typography** | **Standard.** High weight contrast between display and body text. Variable font weight axes exploited for hierarchy without extra file requests. |
| **Dark Mode First** | **Standard.** All palettes designed for dark environment first; `light-dark()` lifts to light mode. Reduces eye strain and OLED energy consumption. |
| **Mesh / Aurora Gradients** | **CSS-only.** Stack multiple `radial-gradient` declarations in `background-image` using OKLCH colors. Blend with `mix-blend-mode`. Optional SVG noise texture overlay. No WebGL/Canvas JS. |

⚠️ **Anti-pattern: Glassmorphism over text content**
Do NOT apply glassmorphism (`backdrop-filter: blur`) over paragraphs or reading text. The blur reduces legibility and violates WCAG 2.2 contrast requirements. Glassmorphism is for overlays, navbars, and menus only.

⚠️ **Anti-pattern: Neumorphism in any form**
Do NOT use neumorphism (extruded shadows, dual opposing highlights) under any circumstances. It is not merely "outdated" — it actively violates EAA 2025 contrast thresholds and destroys shape recognition for visually impaired users.

**Decision Tree: Which visual trend applies?**

```
if building dashboard with metrics of varying importance → Bento Grid (mandatory)
if overlay above content needs depth (modal, menu, sticky nav) → Glassmorphism (focalized)
if decorative background for hero or landing page → Mesh / Aurora Gradients (CSS-only)
if dark mode is expected by users → Dark Mode First (design dark first; light-dark() lifts)
if legacy system uses neumorphism → Remove immediately; refactor to flat + shadow tokens
```

---

## 8. Performance Visual

### Containment & Visibility

| Property | Declaration | Performance Impact |
|----------|-------------|-------------------|
| **content-visibility** | `content-visibility: auto;` + `contain-intrinsic-size` | Omits layout and paint for below-the-fold trees until near viewport. |
| **contain** | `contain: paint layout;` | Isolates mutations. Internal changes do not recalculate parent or siblings. |
| **will-change** | `will-change: transform, opacity;` (transient only) | Promotes element to GPU compositor layer before complex animation. Remove after animation completes. |

### Cumulative Layout Shift (CLS) Prevention

Load fonts asynchronously. Compensate FOUT/FOIT with `font-size-adjust`. Declare system UI font stack as algorithmic fallback. Reserve exact space for images with `aspect-ratio` before load.

### Image Format Decision

| Format | Adoption (May 2026) | Verdict |
|--------|---------------------|---------|
| **WebP** | ~95.6% | Default for user-generated content (thumbnails). Fast encode (~90ms). |
| **AVIF** | ~93.8% browser / ~1.3% real delivery | Static assets only (logos, UI graphics). ~50% smaller than JPEG, but encode is slow (~1–2s). |
| **JPEG XL** | <0.1% | Prohibited for direct client interfaces. Insufficient WebKit/iOS support despite superior compression. |

⚠️ **Anti-pattern: AVIF for user-generated thumbnails**
Do NOT encode user-uploaded images to AVIF on-the-fly. The encode time (~1–2s per image) blocks the upload pipeline and degrades UX. Use WebP for dynamic content; reserve AVIF for static assets generated at build time.

⚠️ **Anti-pattern: will-change left permanently**
Do NOT leave `will-change: transform` on elements indefinitely. It promotes the element to a GPU layer, consuming VRAM. Apply `will-change` 200ms before animation; remove it in the `transitionend` / `animationend` handler.

**Decision Tree: Which performance optimization?**

```
if section is below the fold (not in initial viewport) → content-visibility: auto
if component mutates frequently (animations, resizes) → contain: paint layout
if animation is about to start (user clicked, scroll triggered) → will-change: transform, opacity (transient)
if font loading causes layout shift → font-size-adjust + system-ui fallback
if image format must be chosen → WebP for dynamic, AVIF for static build, JPEG XL prohibited
```

---

## 9. Design.md (Google Labs)

Launched April 2026 by Google Labs. Replaces Figma handoff with a deterministic, AI-consumable design specification.

- **YAML Front Matter**: Machine-readable metadata. Declares design system structure: OKLCH colors, fluid typography scales (`cqi`), spacing grid, border-radius hierarchy.
- **Markdown Prose**: Narrative rules for LLMs. Explains *why* and *when* to invoke the YAML tokens. Example: "The destructive error palette is implemented on modals and notifications only; never on passive interactive elements like tabs."

**Toolchain**: Design traced in Figma/Google Stitch → auto-generate `design.md` → Claude Code consumes via MCP → deterministic component generation without per-request contrast micromanagement.

⚠️ **Anti-pattern: Design.md without narrative rules**
Do NOT emit a `design.md` with only YAML front matter and no Markdown prose. The YAML declares *what* the tokens are; the prose explains *when* and *why* to invoke them. Without prose, an AI agent will apply tokens indiscriminately (e.g., using the destructive error palette on passive tabs).

⚠️ **Anti-pattern: Copying Design.md between projects without adaptation**
Do NOT copy a `design.md` verbatim from one project to another. Every project has unique brand hues, spacing scales, and accessibility constraints. Adapt the primitives; keep the prose structure.

**Decision Tree: Should I create a Design.md?**

```
if project has ≥3 developers touching CSS → Yes: Design.md prevents token drift
if project uses AI agents for component generation → Yes: Design.md is the MCP contract
if project is a single-page prototype with <5 components → No: inline tokens suffice
if project already has a living design system (Figma tokens exported) → Yes: auto-generate from Figma
```

---

## 10. Staff-Level Snippet

Production-ready HTML5 + CSS consolidating `@layer`, three-tier tokens, `light-dark()`, bento grid, container queries, and dark mode.

```html
<section class="bento-dashboard" aria-label="Performance Metrics Panel">
  <article class="stat-card stat-card--hero">
    <header class="stat-card__header">
      <h2 class="stat-card__title">Cyclic Return</h2>
      <span class="stat-card__status" data-state="success" aria-label="Positive"></span>
    </header>
    <div class="stat-card__content">
      <p class="stat-card__value">42.8%</p>
      <p class="stat-card__metric">+5.2% vs prior baseline.</p>
    </div>
  </article>
  <article class="stat-card">
    <header class="stat-card__header">
      <h2 class="stat-card__title">Operational Volume</h2>
      <span class="stat-card__status" data-state="neutral" aria-label="Stable"></span>
    </header>
    <div class="stat-card__content">
      <p class="stat-card__value">1.42M</p>
      <p class="stat-card__metric">Consolidated transactions.</p>
    </div>
  </article>
</section>
```

```css
@layer tokens, reset, layout, components, utilities;

@layer tokens {
  :root {
    --p-hue-brand: 250;
    --p-brand-light: oklch(0.95 0.05 var(--p-hue-brand));
    --p-brand-base:  oklch(0.60 0.15 var(--p-hue-brand));
    --p-brand-dark:  oklch(0.25 0.10 var(--p-hue-brand));
    --p-surface-white: oklch(0.99 0.01 var(--p-hue-brand));
    --p-surface-black: oklch(0.15 0.02 var(--p-hue-brand));
    --p-space-8: 0.5rem; --p-space-16: 1rem; --p-space-24: 1.5rem;
    color-scheme: light dark;
    --s-bg-canvas:  light-dark(var(--p-surface-white), var(--p-surface-black));
    --s-bg-surface: light-dark(oklch(1 0 0), oklch(0.20 0.02 var(--p-hue-brand)));
    --s-text-primary: light-dark(var(--p-surface-black), var(--p-surface-white));
    --s-text-muted:   light-dark(oklch(0.4 0.05 var(--p-hue-brand)), oklch(0.7 0.05 var(--p-hue-brand)));
    --s-border-radius-base: 16px;
    --s-font-size-fluid: clamp(1rem, 0.8rem + 1cqi, 1.25rem);
  }
}

@layer reset {
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background-color: var(--s-bg-canvas); color: var(--s-text-primary);
    font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    -webkit-font-smoothing: antialiased;
  }
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important; animation-iteration-count: 1 !important;
      transition-duration: 0.01ms !important; scroll-behavior: auto !important;
    }
  }
}

@layer layout {
  .bento-dashboard {
    display: grid; grid-template-columns: repeat(12, 1fr);
    gap: var(--p-space-24); padding: var(--p-space-24);
    max-width: 1440px; margin-inline: auto;
  }
  .stat-card { grid-column: span 12; }
  @media (min-width: 768px) {
    .stat-card { grid-column: span 4; }
    .stat-card--hero { grid-column: span 8; }
  }
}

@layer components {
  .stat-card {
    container-type: inline-size; container-name: card-container;
    --c-card-bg: var(--s-bg-surface); --c-card-radius: var(--s-border-radius-base);
    background-color: var(--c-card-bg); border-radius: var(--c-card-radius);
    padding: var(--p-space-24);
    border: 1px solid light-dark(oklch(0.9 0 0), oklch(0.3 0 0));
    &:focus-visible { outline: 2px solid var(--p-brand-base); outline-offset: 4px; }
  }
  .stat-card__header {
    display: flex; justify-content: space-between; align-items: flex-start;
    margin-block-end: var(--p-space-16);
  }
  .stat-card__title { font-size: var(--s-font-size-fluid); font-weight: 600; }
  .stat-card__value {
    color: oklch(from var(--p-brand-base) calc(l - 0.1) c h);
    font-size: clamp(2rem, 1.5rem + 5cqi, 3.5rem);
    font-weight: 800; letter-spacing: -0.02em; line-height: 1;
  }
  .stat-card__metric { color: var(--s-text-muted); font-size: 0.875rem; margin-block-start: var(--p-space-8); }
  .stat-card__status[data-state="success"] {
    width: 12px; height: 12px; border-radius: 50%;
    background-color: oklch(0.7 0.2 146); box-shadow: 0 0 0 4px oklch(0.7 0.2 146 / 0.15);
  }
  .stat-card__status[data-state="neutral"] {
    width: 12px; height: 12px; border-radius: 50%;
    background-color: oklch(0.7 0.05 250); box-shadow: 0 0 0 4px oklch(0.7 0.05 250 / 0.15);
  }
  @container card-container (max-width: 320px) {
    .stat-card__header { flex-direction: column; gap: var(--p-space-8); }
    .stat-card__status { align-self: flex-end; }
    .stat-card__value { font-size: 2rem; }
  }
}
```

---

## 11. Decision Framework

A 10-step decision tree for frontend visual layer design. Each step resolves a binary choice that narrows the applicable rule set.

```
Step 1: Is this a reusable component or a one-off?
  → Reusable → container-type: inline-size; NO external margins; @container for internals
  → One-off → Document context; margins acceptable if parent grid does not govern

Step 2: Does the component need internal spatial reconfiguration?
  → Yes → @container query (Component-First)
  → No → Fixed layout; parent grid governs placement

Step 3: Is this a macro page layout or internal component layout?
  → Macro page (header, sidebar, footer) → CSS Grid + @media acceptable
  → Internal component (card, modal, form) → @container mandatory; @media prohibited

Step 4: Are you defining a color for the design system?
  → Yes (primitive token) → OKLCH only; ban HEX/RGB/HSL in --p-* variables
  → No (semantic/component token) → Reference primitive; use light-dark() for dual-mode

Step 5: Does the value change between light and dark modes?
  → Yes → Tier 2 Semantic token with light-dark()
  → No → Tier 1 Primitive token (static across modes)

Step 6: Is the element interactive or passive reading?
  → Interactive (button, link, input) → 44×44 touch target; 2px focus; 3:1 adjacent contrast
  → Passive reading (paragraph, heading) → 4.5:1 contrast; 60–75ch line width

Step 7: Does the component need animation?
  → User-triggered (hover, click) → CSS transition on transform/opacity only
  → Scroll-triggered (reveal, parallax) → animation-timeline: scroll() / view()
  → Complex dynamic orchestration → WAAPI (JS); CSS declarations stay declarative
  → Loading state → Skeleton with linear-gradient animation (CSS only)

Step 8: Is the animation purely decorative or structural?
  → Decorative (parallax, reveal) → MUST use animation-timeline; NO IntersectionObserver
  → Structural (dropdown expand, accordion) → grid-template-rows: 0fr → 1fr transition

Step 9: Is the content below the fold (outside initial viewport)?
  → Yes → content-visibility: auto; contain-intrinsic-size for space reservation
  → No (above the fold) → Standard rendering; no deferral needed

Step 10: Are you choosing an image format?
  → User-generated / dynamic content → WebP (fast encode, broad support)
  → Static UI asset (logo, icon) → AVIF (superior compression; encode at build time)
  → Client-facing delivery → JPEG XL prohibited (insufficient WebKit/iOS support)
```

### Quick Reference: Verdict by Context

| Context | Rule |
|---------|------|
| Reusable card | `container-type: inline-size`; no margin; `@container` for internals |
| One-off landing hero | Contextual margins acceptable; macro `@media` for layout |
| Color token | OKLCH; no HEX/RGB/HSL in primitives |
| Interactive element | 44×44 px; 2px outline; 3:1 adjacent contrast |
| Scroll animation | `animation-timeline`; never `IntersectionObserver` for decoration |
| Below-the-fold section | `content-visibility: auto` + `contain-intrinsic-size` |
| Dynamic image | WebP; AVIF only for build-time static assets |

⚠️ **Anti-pattern: Applying the framework to framework decisions**
This Decision Framework governs **visual layer design only** (CSS, tokens, layout, animation). It does NOT choose between React, Vue, Svelte, or Angular. Framework selection is the domain of `frontend-architect`. Do NOT extend this tree with framework branches.

---

## Commands

```bash
# Verify contrast ratios (OKLCH-aware)
npx @adobe/spectrum-css-contrast-checker --token-file tokens.css

# Lighthouse accessibility + performance audit
npx lighthouse https://example.com --only-categories=accessibility,performance

# CSS specificity visualization
npx specificity-graph src/styles.css

# Validate CSS nesting and layer support
npx browserslist "supports css-nesting and supports css-cascade-layers"

# AVIF encoding for static assets
avifenc --min 0 --max 63 --minalpha 0 --maxalpha 63 -a end-usage=q -a cq-level=18 -a tune=ssim input.png output.avif

# Check WCAG 2.2 compliance via axe-core
npx axe-core-cli https://example.com --tags wcag22aa
```

---

## Resources

- **CSS Nesting Module Level 1**: https://www.w3.org/TR/css-nesting-1/
- **OKLCH in CSS — Evil Martians**: https://evilmartians.com/chronicles/oklch-in-css-why-quit-rgb-hsl
- **MDN Container Queries**: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries
- **CSS Scroll-Driven Animations**: https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Scroll-driven_animations
- **WCAG 2.2 Overview**: https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/
- **European Accessibility Act 2025 — Chromatic**: https://www.chromatic.com/blog/developers-guide-to-european-accessibility-act-2025/
- **Design.md Spec (Google Labs)**: https://design.md (reference implementation)
- **Bento Grid CSS Guide**: https://senorit.de/en/blog/bento-grid-design-trend-2025
