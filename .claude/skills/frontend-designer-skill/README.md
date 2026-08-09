# 🎨 frontend-designer-skill

> **Framework-agnostic** frontend design skill for **UI/UX architecture**.
> Covers CSS layers, container queries, OKLCH design tokens, component patterns,
> WCAG 2.2 + EAA 2025 accessibility, scroll-driven animations, visual trends 2026,
> and local `design.md` handoff patterns for AI design-to-code.
> Based on real ecosystem research validated against live sources (May 2026).

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

## Why This Exists

AI agents (Cursor, Claude Code, Copilot) now consume our codebases directly. A poorly structured `SKILL.md` causes agents to hallucinate Tailwind defaults, propose deprecated BEM patterns, omit accessibility compliance, or generate non-deterministic color systems.

This skill is a **validated, opinionated reference** for frontend visual layer decisions — covering CSS architecture, design tokens, component patterns, accessibility mandates, animation strategy, performance visual, and the local `design.md` AI-consumable handoff pattern.

Built from a 496-line research document analyzing the 2025–2026 frontend ecosystem (cascade layers, OKLCH, container queries, EAA 2025, bento grids), then cross-checked against live sources (W3C specifications, MDN, Evil Martians, Chromatic).

---

## 📦 Versions

| Version                    | File                                                         | Size                   | When to Use                                                                         |
| -------------------------- | ------------------------------------------------------------ | ---------------------- | ----------------------------------------------------------------------------------- |
| **v3.0** (Current)         | [`versions/v3.0/SKILL.md`](versions/v3.0/SKILL.md)           | ~55 lines + references | Compact runtime skill with curated `references/` and May 2026 source index          |
| **v1.0** (Historical)      | [`versions/v1.0/SKILL.md`](versions/v1.0/SKILL.md)           | ~678 lines             | Preserved for backward compatibility; verify claims against v3 before reuse         |
| **v1.0-lite** (Historical) | [`versions/v1.0-lite/SKILL.md`](versions/v1.0-lite/SKILL.md) | ~352 lines             | Preserved for backward compatibility; v3.0 replaces it for active runtime ingestion |

### What's New in v3.0

- ⚠️ Anti-pattern blocks (`⚠️ Anti-pattern:` prefix) in every section
- 🌳 Decision trees (`if X → Y`) for CSS architecture, token selection, and component choice
- 📐 Decision Framework (10-step ASCII decision tree) replacing static Architectural Dictates
- 🎨 Local `design.md` handoff pattern with full token YAML + narrative rules
- 🔧 `.github/workflows/pr-validation.yml` enforcing issue-first + type-label discipline
- 📋 `.github/pull_request_template.md` aligned with workspace conventions
- 🏷️ GitHub Topics (≥15) for discoverability

### What's Covered in v3.0

- ✅ **CSS Architecture 2026** — `@layer` cascade layers, native nesting, `:has()`, BEM obsolescence table
- ✅ **Design Tokens (3-Tier)** — Primitive → Semantic → Component, OKLCH mandatory, fluid typography (`clamp` + `cqi`), 8/4-point spacing
- ✅ **Component Patterns** — 5-responsibility model, card/modal/table/form/nav/hero/bento with container queries
- ✅ **Responsive & Layout** — Subgrid, named areas, `aspect-ratio`, content-based breakpoints
- ✅ **Accessibility** — WCAG 2.2 + EAA 2025, contrast 4.5:1 / 3:1, 2px focus, 24×24 CSS px AA target minimum with 44×44 px enhanced touch baseline, `prefers-reduced-motion`
- ✅ **Micro-interactions** — CSS transitions preferred, compositor-only properties, scroll-driven animations, hover without `:hover`
- ✅ **Visual Trends 2026** — Bento grids, constrained glassmorphism, neumorphism treated as high-risk for interactive UI, mesh/aurora gradients CSS-only
- ✅ **Performance Visual** — `content-visibility`, `contain`, AVIF/WebP table, variable fonts, CLS prevention
- ✅ **design.md** — local AI design-to-code handoff pattern, YAML + Markdown, reviewable token/state contract
- ✅ **Staff-Level Snippet** — Full HTML+CSS example with `@layer`, tokens, bento grid, dark mode
- ✅ **Architectural Dictates** — 5 non-negotiable rules for component independence, viewport rejection, OKLCH-only, EAA compliance, zero main-thread scroll orchestration

---

## 🚀 Quick Start

### For AI Agents (Cursor, Claude Code, etc.)

```bash
# Clone into your skills directory
git clone https://github.com/kozz36/frontend-designer-skill.git

# Use the version that matches your need:
# - Full  → detailed token systems, accessibility audits, full snippet
# - Lite  → rapid layout decisions, component patterns, quick checks
```

### For Human Architects

Open `versions/v3.0/SKILL.md` for the runtime contract, then use `versions/v3.0/references/technical-reference.md` for detailed matrices. Key reference areas:

- **Section 1** — CSS Architecture (`@layer`, nesting, `:has()`)
- **Section 2** — Design Tokens (OKLCH, fluid typography, dark mode)
- **Section 5** — Accessibility (contrast, focus, EAA 2025)
- **Section 10** — Staff-Level Snippet (copy-paste production CSS)
- **Section 11** — Architectural Dictates (non-negotiable rules)

---

## 📁 Structure

```
versions/
├── v1.0/
│   └── SKILL.md              # Historical full reference
├── v1.0-lite/
│   └── SKILL.md              # Historical condensed reference
└── v3.0/
    ├── SKILL.md              # Current compact runtime contract
    └── references/
        ├── technical-reference.md
        └── source-index.md
docs/
├── CHANGELOG.md              # Verified version history
└── CONTRIBUTING.md           # How to contribute improvements
```

---

## 🔍 Verification Methodology

Every claim was validated against authoritative sources:

| Claim                                                      | Verification Method                             | Status                                                                                                                 |
| ---------------------------------------------------------- | ----------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| CSS `@layer` resolves specificity deterministically        | W3C CSS Cascade Layers spec                     | ✅ Confirmed                                                                                                           |
| Native CSS Nesting is production-ready (no preprocessor)   | W3C CSS Nesting Module Level 1                  | ✅ Confirmed                                                                                                           |
| `:has()` supported universally (2024+)                     | MDN + Can I Use data                            | ✅ Confirmed                                                                                                           |
| OKLCH perceptually uniform vs HSL                          | Evil Martians + CSS Color Module Level 5        | ✅ Confirmed                                                                                                           |
| Container queries replace media queries for components     | MDN Container Queries + LogRocket 2026 analysis | ✅ Confirmed                                                                                                           |
| WCAG 2.2 focus indicator: 2px CSS minimum                  | W3C Understanding SC 2.4.13                     | ✅ Confirmed                                                                                                           |
| EAA 2025 enforceable since June 2025                       | Chromatic + Telerik + WCAG.com                  | ✅ Confirmed                                                                                                           |
| `animation-timeline: scroll()` / `view()` production-ready | MDN Scroll-Driven Animations                    | ✅ Confirmed                                                                                                           |
| Neumorphism risk                                           | WCAG contrast/affordance review                 | ⚠️ Not a legal category; avoid for interactive UI unless constraints prove contrast, focus, and affordance compliance. |
| `light-dark()` CSS function native support                 | MDN + browser baseline 2024                     | ✅ Confirmed                                                                                                           |
| design.md local handoff pattern                            | Local project artifact                          | ⚠️ Pattern, not external standard                                                                                      |
| Subgrid production-ready in all modern engines             | MDN + Can I Use                                 | ✅ Confirmed                                                                                                           |

---

## 🤝 Contributing

This skill is maintained as a living document. See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md) for:

- How to propose additions (new CSS features, updated browser support)
- Verification requirements before merging
- Style guide (tables > narrative, decision trees > lists, prohibitions explicit)

---

## 📝 License

Apache-2.0

---

**Maintained by:** [@kozz36](https://github.com/kozz36)  
**Research base:** "Investigación Técnica: Arquitectura y Diseño Frontend Framework-Agnóstico (2026)" (496-line ecosystem analysis, May 2026)
