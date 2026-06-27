# Design Review & Critique

Use this when reviewing, QA-ing, or improving an existing UI. Give **prioritized, specific, actionable** feedback: each point names *what* is wrong, *why* it matters, and *the fix*. Avoid vague praise ("looks clean") and avoid dumping every nit at equal weight.

## How to deliver a critique

1. **State what works** briefly and specifically (anchor the good so it's preserved).
2. **Group issues by severity:**
   - **Blocker** — broken, inaccessible, or unusable (no focus states, failing contrast, dead-end flow).
   - **Major** — clear usability/clarity/hierarchy problems that hurt the primary task.
   - **Minor** — polish: spacing inconsistencies, alignment, copy.
3. For each: **what → why → fix** (and where, if a specific element).
4. End with the **top 3 highest-leverage changes** so the person knows where to start.

## Nielsen's 10 heuristics (evaluation lens)

1. **Visibility of system status** — does the UI always show what's happening (loading, saved, selected)?
2. **Match to the real world** — language, icons, and order match user expectations, not internal jargon.
3. **User control & freedom** — clear exits, undo/redo, cancel; no traps.
4. **Consistency & standards** — same thing looks/behaves the same; follows platform conventions.
5. **Error prevention** — prevent mistakes (constraints, confirmations, good defaults) over just handling them.
6. **Recognition over recall** — options and info are visible; don't make users remember across screens.
7. **Flexibility & efficiency** — shortcuts/accelerators for experts without blocking novices.
8. **Aesthetic & minimalist design** — every element earns its place; no competing noise.
9. **Help users with errors** — plain-language messages, precise, constructive, with a way out.
10. **Help & documentation** — discoverable guidance where needed (empty states, tooltips, docs).

## Visual-craft checklist

- **Hierarchy:** is the primary action/content obviously dominant? One focal point per section?
- **Type:** sizes from a scale? body 15–17px? line length 60–75ch? consistent weights?
- **Spacing:** from a single scale? proximity grouping correct? consistent section rhythm?
- **Alignment:** everything aligned to a grid/shared edge? numbers right-aligned?
- **Color:** restrained palette? accent reserved for primary emphasis? no saturated large fills?
- **Contrast:** text and UI meet AA? muted text still legible?
- **Components:** radius/elevation consistent? not over-using borders+cards+shadows?
- **States:** hover/focus/active/disabled/loading/empty/error all designed?
- **Consistency:** repeated patterns identical? same term for same thing?
- **Motion:** purposeful, 150–250ms, reduced-motion honored?
- **Responsive:** reflows cleanly; targets ≥ 44px; no clipping at 200% zoom.
- **Microcopy:** buttons named by outcome; errors say what+why+fix; empty states orient.

## Quick scoring (optional)

Rate each 1–5 to surface the weakest area fast:
`Hierarchy · Typography · Spacing/Alignment · Color/Contrast · Consistency · States · Accessibility · Microcopy · Responsiveness`
Lowest scores = where to spend effort first.

## The "why does this look amateur?" shortlist

When something looks off but the cause isn't obvious, check these in order — they cause most cases:
1. Inconsistent spacing / off-grid alignment.
2. Too many type sizes/weights; body too small or too large.
3. Weak or missing hierarchy (everything same size/weight).
4. Over-saturated or too many colors; accent overused.
5. Boxes/borders/shadows substituting for hierarchy.
6. No empty/loading/error states; only the happy path.
7. Cramped or, conversely, aimless whitespace.
8. Missing focus states and low-contrast text.
