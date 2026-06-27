# Layout & Composition

Layout is where "clean" is won or lost. Alignment, spacing, and a clear hierarchy do more for perceived quality than any color or effect.

## Contents
- Hierarchy
- Grid & alignment
- Spacing rhythm
- Composition patterns
- Responsive strategy

---

## Hierarchy

Before styling, rank the content: **primary** (the one thing this screen is for), **secondary** (supporting), **tertiary** (metadata, utilities). Then make the ranking *obvious* using, in order of preference:

1. **Size** — bigger = more important.
2. **Weight** — heavier draws the eye.
3. **Color / contrast** — strong vs muted; accent for the single most important action.
4. **Position** — top-left and center get seen first (LTR reading); above the fold.
5. **Space** — isolation creates emphasis; whitespace around an element elevates it.

Only *then* reach for borders, cards, or backgrounds. A common amateur move is boxing everything to fake structure that hierarchy should provide. One primary action per screen; if everything is emphasized, nothing is.

## Grid & alignment

- Use a grid (e.g. 12-column on web) or at least consistent shared edges. Every element should align to *something*.
- Establish a max content width (e.g. 1100–1280px for marketing, 640–760px for reading) and consistent gutters.
- Left-align body text and most UI. Center only short, symmetrical content (hero headline, empty states) — never long paragraphs.
- Optical alignment beats mathematical when they disagree (icons, punctuation, round shapes often need a nudge).
- Align numbers right in tables; align labels and values to a consistent column.

## Spacing rhythm

- Use the spacing scale from the visual system everywhere — no free-hand margins.
- **Proximity = grouping:** tight gaps inside a group, larger gaps between groups. This alone makes dense screens legible.
- Keep consistent section padding vertically; don't let some sections breathe and others suffocate.
- Match horizontal and vertical rhythm so the page feels like one system.

## Composition patterns

- **Z-pattern / F-pattern:** marketing scans in a Z; text-dense pages scan in an F. Place key elements on the path.
- **Rule of proximity & similarity:** related controls look alike and sit together; different functions look and sit apart.
- **Visual anchor:** give each section one focal point (a heading, an image, a number), not three competing ones.
- **Asymmetry with balance:** strict symmetry can feel static; balance weight instead (a large image offset by a block of text).
- **Repetition:** repeat the same card, the same header treatment, the same spacing — repetition reads as "designed."
- **Don't fill every pixel:** negative space is structure, not waste.

## Responsive strategy

- Design **mobile-first**: start at the smallest viewport, then add columns/space as width grows. It forces priority decisions.
- Standard breakpoints (adjust to content, not devices): ~480, 768, 1024, 1280px.
- Reflow, don't shrink: multi-column → single column; horizontal nav → menu/drawer; side-by-side → stacked.
- Tap targets ≥ 44×44px on touch; spacing between targets to prevent mis-taps.
- Fluid type and spacing with `clamp()` so scaling is smooth between breakpoints.
- Test the real failure points: long names, empty data, tiny screens, huge screens, RTL if relevant.
- Content priority can change by viewport — it's fine to hide/collapse tertiary content on mobile, never the primary action.
