# Changelog

## [v3.0] - 2026-05-15

### Added

- Added `versions/v3.0/SKILL.md` using the skill-creator compact runtime contract.
- Added `versions/v3.0/references/technical-reference.md` as the curated v3.0 technical basis.
- Added `versions/v3.0/references/source-index.md` for source links and verification status.

### Changed

- Validated v3.0 technical reference against current May 2026 sources and corrected stale or over-strong claims.
- Curated v3.0 references so they are robust local technical bases rather than historical lite dumps.
- Preserved existing lite versions for backward compatibility; v3.0 is the new references-based skill version.

## [v1.0] — 2026-05-02

### Added

- **CSS Architecture 2026** — `@layer` cascade layers, native nesting (`&`), `:has()` relational selector, BEM obsolescence table
- **Design Tokens (3-Tier)** — Primitive (`--p-`), Semantic (`--s-`), Component (`--c-`) tiers; OKLCH mandatory; fluid typography (`clamp` + `cqi`); 8/4-point spacing grid
- **Component Patterns** — 5-responsibility model (Layout, Skin, Typography, Animation, State); Card, Modal, Table, Form, Nav, Hero, Bento grid specifications
- **Responsive & Layout** — Subgrid, named `grid-template-areas`, `aspect-ratio`, content-based breakpoints via `@container`
- **Accessibility (WCAG 2.2 + EAA 2025)** — Contrast ratios (4.5:1 / 3:1), 2px focus indicators, 44×44 px touch targets, `prefers-reduced-motion`, ARIA minimalism
- **Micro-interactions & Animation** — CSS transitions preferred, compositor-only properties (`transform`, `opacity`), scroll-driven animations (`animation-timeline: scroll()` / `view()`), hover without `:hover`
- **Visual Trends 2026** — Bento grids CSS, glassmorphism (focalized), neumorphism documented as high-risk for interactive UI, mesh/aurora gradients CSS-only
- **Performance Visual** — `content-visibility`, `contain`, `will-change` (transient), AVIF/WebP/JPEG XL format decision table, variable fonts, CLS prevention
- **design.md** — local AI design-to-code handoff pattern (YAML front matter + Markdown prose)
- **Staff-Level Snippet** — Production HTML+CSS example consolidating `@layer`, tokens, bento grid, container queries, dark mode
- **Architectural Dictates** — 5 non-negotiable rules for component independence, viewport rejection, OKLCH-only, EAA compliance, zero main-thread scroll orchestration

### Infrastructure

- `.github/workflows/pr-validation.yml` — Automated PR validation enforcing issue linkage + type-label checks
- `.github/pull_request_template.md` — Aligned with workspace issue-first conventions
- `design.md` — Local design handoff demonstration with full token YAML + narrative rules

### Verifications

All claims validated via live sources (May 2026):

- ✅ W3C CSS Cascade Layers spec — `https://www.w3.org/TR/css-cascade-5/#layering` — `@layer` specificity determinism confirmed
- ✅ W3C CSS Nesting Module Level 1 — `https://www.w3.org/TR/css-nesting-1/` — native `&` production-ready confirmed
- ✅ MDN `:has()` — `https://developer.mozilla.org/en-US/docs/Web/CSS/:has` — universal support (2024+) confirmed
- ✅ Evil Martians OKLCH — `https://evilmartians.com/chronicles/oklch-in-css-why-quit-rgb-hsl` — perceptual uniformity confirmed
- ✅ MDN Container Queries — `https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_container_queries` — `cqi` production-ready confirmed
- ✅ W3C WCAG 2.2 SC 2.4.13 — `https://www.w3.org/WAI/WCAG22/Understanding/focus-appearance.html` — 2px focus minimum confirmed
- ✅ Chromatic EAA 2025 — `https://www.chromatic.com/blog/developers-guide-to-european-accessibility-act-2025/` — enforceable June 2025 confirmed
- ✅ MDN Scroll-Driven Animations — `https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Scroll-driven_animations` — `animation-timeline` production-ready confirmed
- ✅ UX Planet Neumorphism — `https://uxplanet.org/the-rise-and-fall-of-neumorphism-613795fc6f8d` — EAA contrast violation confirmed
- ✅ MDN `light-dark()` — `https://developer.mozilla.org/en-US/docs/Web/CSS/color_value/light-dark` — native support confirmed
- ⚠️ design.md — local project artifact — handoff pattern, not external standard
- ✅ MDN Subgrid — `https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Subgrid` — all modern engines confirmed
