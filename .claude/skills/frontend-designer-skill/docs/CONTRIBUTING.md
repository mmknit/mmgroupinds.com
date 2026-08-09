# Contributing to frontend-designer-skill

This skill is a **living document**. The CSS ecosystem mutates quarterly as browsers ship new features. Stale information is worse than no information.

## What We Accept

| Change Type | Example | Likelihood of Merge |
|-------------|---------|---------------------|
| **New CSS feature shipped** | "CSS `attr()` with fallback is now baseline" | High (with spec link) |
| **Browser support shift** | "Subgrid now in Safari Tech Preview" | High (with Can I Use link) |
| **Accessibility law update** | "EAA 2025 enforcement extended to sector X" | High (with regulatory link) |
| **New visual pattern validated** | "CSS anchor positioning production-ready" | Medium (needs rationale) |
| **Correction** | "OKLCH relative color syntax requires `from`" | High (with proof) |
| **Rewrite for readability** | "Section X is unclear" | Medium (must keep tables) |
| **Style-only** | "Better wording" | Low (unless ambiguous) |

## What We Reject

- ❌ Unverified browser support claims ("I heard Firefox will ship X")
- ❌ Narrative-heavy additions ("The landscape has shifted dramatically...")
- ❌ Vendor marketing copy ("Revolutionary breakthrough...")
- ❌ Breaking structural changes without discussion first

## Style Guide

This is a skill for **AI agents**, not a blog post.

### Format Rules

1. **Tables > bullet lists** for comparisons
2. **Decision trees (`if X then Y`)** over paragraphs
3. **Prohibitions (`Do NOT`, `Red Flags`)** must be explicit
4. **One concept per section** — no digressions
5. **Links to specifications** for every browser support or spec claim

### Tone

- Direct. Technical. Zero marketing.
- "Avoid" → say why (reflow, paint, accessibility violation)
- "Prefer" → say when the exception applies

## Verification Requirements

Every claim about a **browser feature, spec status, or accessibility law** must include one of:

- Link to W3C specification or Working Draft
- Link to MDN compatibility table
- Link to Can I Use data
- Link to official regulatory publication (EAA, WCAG, EN 301 549)

## How to Submit

### Pull Request Workflow (REQUIRED)

This repo follows the **issue-first, PR-mandatory** workflow, enforced by `.github/workflows/pr-validation.yml`:

```
1. Open an issue describing the change
2. Wait for `status:approved` label (maintainer review)
3. Create branch: `type/description` (e.g., `feat/subgrid-section`, `fix/oklch-syntax`)
4. Implement changes with conventional commits
5. Update `docs/CHANGELOG.md`
6. Open PR with `Closes #N` in body (use `.github/pull_request_template.md` as reference)
7. Add exactly one `type:*` label
8. Wait for automated checks to pass (issue linkage + type label validation)
9. Maintainer merges
```

**NEVER push directly to `main`.** Direct pushes are blocked by branch protection.

### Branch Naming

```
^(feat|fix|chore|docs|style|refactor|perf|test|build|ci|revert)\/[a-z0-9._-]+$
```

| Type | Example |
|------|---------|
| `feat/` | `feat/anchor-positioning-section` |
| `fix/` | `fix/focus-indicator-contrast` |
| `docs/` | `docs/wcag-2.2-update` |
| `chore/` | `chore/update-browser-support-tables` |

### Conventional Commits

Format: `type(scope): description`

| Type | PR Label | When |
|------|----------|------|
| `feat` | `type:feature` | New section or capability |
| `fix` | `type:bug` | Correction or error |
| `docs` | `type:docs` | Documentation change |
| `refactor` | `type:refactor` | Restructure without content change |
| `chore` | `type:chore` | Maintenance, tool updates |

### Commit Checklist

- [ ] Linked an approved issue (`Closes #N`)
- [ ] Added exactly one `type:*` label
- [ ] Conventional commit format
- [ ] No `Co-Authored-By` trailers
- [ ] Spec/MDN verification included for feature claims
- [ ] `docs/CHANGELOG.md` updated

Original direct-push workflow (DEPRECATED — kept for historical reference):

1. Fork the repo
2. Edit the relevant `SKILL.md`
3. Update `docs/CHANGELOG.md`
4. ~~Push to main~~ → **Use PR workflow above**

## Version Policy

- **Patch (x.x.1)** — Corrections, clarifications, link fixes
- **Minor (x.1.0)** — New sections, updated browser support, new patterns
- **Major (2.0.0 → 3.0.0)** — Structural rewrites, new paradigms, breaking recommendations

## Topics for Future Exploration

- CSS anchor positioning and popover API integration
- `@property` registered custom properties for typed animations
- Style queries (`@container style(--theme: dark)`) for component state queries
- View Transitions API cross-document patterns
- CSS `attr()` with fallback for design-to-code automation
- OKLCH relative color advanced algebra (mixing, interpolation)

If you have validated research on any of these, open an issue first to discuss scope.
