---
name: ui-ux-pro-max
description: Apply senior-level UI/UX and visual-design judgment to any interface, layout, component, screen, or frontend. Use whenever building, styling, reviewing, redesigning, or critiquing UIs, design systems, user flows, forms, dashboards, landing pages, or app screens — even when the user only says "make it look professional / cleaner / more polished" and does not explicitly say "design."
---

# UI/UX Pro Max

A senior product-designer brain for any interface work: web, mobile, app, dashboard, marketing, or design-system. The goal is output that looks intentional and considered — never templated, never default — and that is usable, accessible, and consistent.

Apply this skill proactively. If a task produces or changes anything a person looks at or interacts with, the design decisions here are in scope even when the user only asked to "build" or "fix" something.

## Operating principle

Design is decisions, not decoration. Every visual choice (size, weight, color, spacing, position) should encode meaning — hierarchy, grouping, state, or affordance. If a choice can't be justified by one of those, it's noise. The fastest way to look "professional" is **consistency + hierarchy + restraint**, not more effects.

## Workflow

Work in this order. Skipping straight to colors and shadows is the #1 cause of amateur-looking UI.

1. **Purpose & user** — What is this screen *for*, who uses it, and what is the single primary action? Everything else is secondary and should look secondary.
2. **Content & hierarchy** — List the real content blocks in priority order. Establish a clear 1 → 2 → 3 visual hierarchy before styling anything.
3. **Layout & rhythm** — Choose a structure (grid, columns, sections) and a consistent spacing rhythm. Alignment and spacing do 80% of the "clean" work. See `references/layout-composition.md`.
4. **Visual system** — Lock the tokens *first*: type scale, spacing scale, color roles, radius, elevation, motion. Then apply them everywhere. Never hand-pick one-off values. See `references/visual-system.md`.
5. **Components & states** — Design each component for *all* its states, not just the happy path (see State checklist below). See `references/ux-patterns.md`.
6. **Accessibility pass** — Contrast, focus, target size, keyboard, motion, semantics. This is a requirement, not a nice-to-have. See `references/accessibility.md`.
7. **Review** — Critique against heuristics and the checklist before calling it done. See `references/design-review.md`.

When the brief already pins a direction (brand colors, a reference, an existing design system), follow it exactly — the brief's own words win over any default here.

## Pick a point of view

Generic UI is forgettable. Before styling, choose a deliberate aesthetic direction and commit to it consistently: e.g. *editorial / minimal*, *technical / dense*, *warm / approachable*, *premium / restrained*, *bold / expressive*, *brutalist / utilitarian*. The direction governs type, color, spacing, and motion together. Mixing directions is what produces the "default template" feel. One clear voice beats five trendy effects.

## The non-negotiable defaults

These prevent the most common amateur tells:

- **Type scale, not random sizes.** Pick a modular scale (e.g. 12 · 14 · 16 · 20 · 24 · 32 · 48). Body text 15–17px on web. Long-form line length 60–75 characters. Line-height ~1.5 for body, ~1.1–1.25 for large headings.
- **One type family, two at most.** Vary weight and size for hierarchy, not font count. Tighten letter-spacing slightly on large headings; never letter-space lowercase body.
- **A spacing scale, used religiously.** Base unit 4 or 8px (e.g. 4 · 8 · 12 · 16 · 24 · 32 · 48 · 64). Inconsistent gaps are the most visible amateur tell. Group related items with less space, separate groups with more (proximity = meaning).
- **Restrained color.** One neutral ramp (5–8 steps) + one brand/accent + semantic colors (success/warning/danger/info). Use accent sparingly — for the primary action and key emphasis only. Large flat areas of saturated color read cheap; tint and desaturate for surfaces.
- **Hierarchy through contrast, not boxes.** Reach for size, weight, and color/opacity before adding borders, cards, or dividers. Most "boxed-in" UIs over-use borders. When you do use cards, keep radius and elevation consistent.
- **Generous whitespace.** Crowding reads as low-quality. Let primary content breathe; density is a deliberate choice for data-heavy tools, not a default.
- **Alignment.** Everything aligns to a grid or a shared edge. Optical alignment over mathematical when they disagree.
- **Elevation with intent.** Soft, low shadows; consistent light source. Prefer one or two elevation levels over many. Avoid heavy drop shadows and double borders+shadows.
- **Motion that informs.** 150–250ms, ease-out for entrances. Animate to show cause/effect or continuity, never for decoration. Always honor `prefers-reduced-motion`.

## State checklist (every interactive component)

Designing only the default state is the second most common amateur tell. For each component, define:

- **Default · Hover · Focus (visible!) · Active/pressed · Disabled · Loading · Error/invalid · Selected** (where relevant)
- And every container that holds dynamic content: **Empty · Loading/skeleton · Partial · Error · Ideal/full** — design the empty and error states deliberately; they are where products feel unfinished.

## Microcopy

Words are UI. Buttons name the action they perform ("Create project", not "Submit"). Errors say what happened *and* how to fix it. Empty states orient and offer the next step. Be specific, human, and brief. See `references/ux-patterns.md`.

## Common failure modes to avoid

- Styling before hierarchy is settled → "everything shouts, nothing leads."
- Random spacing and sizes → unprofessional even with nice colors.
- Saturated color on large surfaces; rainbow palettes; low-contrast gray-on-gray text.
- Missing focus rings (removing `outline` with no replacement) — breaks keyboard use and accessibility.
- Only the happy path designed — no empty/loading/error states.
- Centered long paragraphs; line lengths > 80ch; tiny touch targets (< 44px).
- Over-using cards, borders, and shadows to fake structure that hierarchy should provide.

## How to respond

- If building UI: state the aesthetic direction and the core tokens briefly, then build consistently against them.
- If reviewing UI: use `references/design-review.md` — give prioritized, specific, actionable feedback (what + why + the fix), not vague praise.
- Decide sensible defaults rather than over-asking; surface genuine forks (brand constraints, audience, density) concisely. One good default beats five questions.

## Reference files

Load the relevant file when a task goes deep in that area:

- `references/visual-system.md` — design tokens in detail: type scale, color systems & ratios, spacing, radius, elevation, motion. Read when establishing or auditing a visual system / theme.
- `references/layout-composition.md` — grids, hierarchy, alignment, responsive breakpoints, composition patterns. Read for page/screen layout.
- `references/ux-patterns.md` — navigation, forms, feedback, data display, states, microcopy patterns. Read for interaction/flow design.
- `references/accessibility.md` — WCAG 2.2 AA practical checklist: contrast, focus, keyboard, targets, motion, semantics, ARIA. Read before finalizing anything.
- `references/design-review.md` — Nielsen heuristics + a structured critique checklist and scoring. Read when reviewing or QA-ing a UI.
