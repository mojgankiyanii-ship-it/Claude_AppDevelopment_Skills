# Visual System — Design Tokens

The visual system is the set of reusable decisions every component draws from. Define these **once**, then never hand-pick a one-off value. Consistency here is what separates professional UI from amateur UI.

## Contents
- Typography
- Color
- Spacing
- Radius
- Elevation (shadow)
- Motion
- Token scaffold (copy-paste starting point)

---

## Typography

**Scale.** Use a modular scale so sizes relate harmonically instead of arbitrarily. Two reliable options:
- Simple, predictable: `12 · 14 · 16 · 20 · 24 · 32 · 40 · 48 · 64`
- Ratio-based (1.25 "major third"): `13 · 16 · 20 · 25 · 31 · 39 · 49`

**Body size.** 15–17px on web; 16px is the safe default (and prevents mobile zoom-on-focus). Captions/labels 12–13px. Don't go below 12px for anything a user must read.

**Line length.** 60–75 characters for paragraphs. Too wide and the eye loses the next line; too narrow and rhythm breaks. Constrain with `max-width: 65ch`.

**Line height.** Body ~1.5. Large headings 1.05–1.25 (tighter as size grows). UI labels ~1.2.

**Weight for hierarchy.** Vary weight (400 / 500 / 600 / 700 / 800) and size, not font family, to build hierarchy. Body 400–500; emphasis 600; headings 600–800.

**Letter-spacing.** Slightly negative on large display headings (-0.01 to -0.03em). Slightly positive on small UPPERCASE labels (+0.04 to +0.08em). Never letter-space lowercase body text.

**Families.** One family is plenty. Two max — typically one for headings, one for body/UI. Ensure the body font has the weights you reference. Good neutral system stacks avoid webfont loading cost.

**Numbers.** Use tabular/lining figures for tables, prices, and data so columns align.

---

## Color

Build a **system of roles**, not a pile of swatches.

**1. Neutral ramp** (the backbone). 6–10 steps from near-white to near-black, slightly tinted toward your brand temperature (warm grays vs cool grays change the whole mood). Used for text, surfaces, borders, dividers.

**2. Brand / accent.** One primary accent, possibly with a hover/active shade. Used sparingly — primary action and key emphasis only. Saturated color over large surfaces reads cheap; for big areas, desaturate and darken/lighten into a "surface" tint.

**3. Semantic.** success (green), warning (amber), danger (red), info (blue) — each with a text shade and a soft background tint shade.

**Assign roles, then map tokens:**
```
--text-strong, --text, --text-muted, --text-on-accent
--surface, --surface-raised, --surface-sunken
--border, --border-strong
--accent, --accent-hover, --accent-contrast
--success / --warning / --danger / --info  (+ each *-bg soft tint)
```

**Contrast (required).** Body text ≥ 4.5:1 against its background; large text (≥24px or ≥19px bold) and UI components/borders ≥ 3:1. Check the muted/gray-on-gray text especially — it's the usual failure. (See accessibility reference.)

**Dark mode.** Don't just invert. Use elevated surfaces that get *lighter* with elevation, reduce pure-white text to ~90% opacity, and desaturate accents slightly so they don't vibrate on dark.

---

## Spacing

One base unit, one scale, applied everywhere. Base **4px** (fine control) or **8px** (simpler).

`4 · 8 · 12 · 16 · 24 · 32 · 48 · 64 · 96 · 128`

- **Proximity encodes grouping.** Related items get small gaps; unrelated groups get large gaps. This single rule makes layouts instantly readable.
- Component padding scales with importance/size (a chip ≠ a hero).
- Vertical rhythm: keep consistent section spacing; don't free-hand margins.
- Inconsistent spacing is the most visible amateur tell — more than color.

---

## Radius

Pick a small set and use consistently: e.g. `0 · 2 · 6 · 10 · 16 · full`. The radius is part of the aesthetic voice (sharp = technical/serious; round = friendly/soft). Keep nested radii visually concentric (outer radius ≥ inner radius + padding). Don't mix many radii randomly.

## Elevation (shadow)

Shadows imply a light source — keep it consistent (usually top-down). Prefer **soft, low-spread, multi-layer** shadows over one harsh blur:
```
--shadow-sm: 0 1px 2px rgba(0,0,0,.06);
--shadow-md: 0 2px 4px rgba(0,0,0,.06), 0 4px 12px rgba(0,0,0,.08);
--shadow-lg: 0 8px 24px rgba(0,0,0,.12);
```
Use 1–3 levels, mapped to meaning (raised card, popover, modal). Never combine a heavy border *and* a heavy shadow on the same element. On dark UIs, elevation reads better as a lighter surface than as a shadow.

## Motion

- Durations: micro 100–150ms, standard 150–250ms, large/page 250–400ms.
- Easing: ease-out for entrances (fast→settle), ease-in for exits, standard ease-in-out for moves. Avoid linear except for spinners.
- Animate transform/opacity (cheap, smooth); avoid animating layout properties.
- Motion must show cause→effect or continuity. No idle decorative motion.
- Always wrap in `@media (prefers-reduced-motion: reduce)` and drop/shorten animation.

---

## Token scaffold (starting point)

```css
:root{
  /* type */
  --font-sans: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, sans-serif;
  --fs-12:.75rem; --fs-14:.875rem; --fs-16:1rem; --fs-20:1.25rem;
  --fs-24:1.5rem; --fs-32:2rem; --fs-48:3rem;
  --lh-tight:1.15; --lh-body:1.5;

  /* neutral ramp (cool example) */
  --n-0:#ffffff; --n-50:#f7f8fa; --n-100:#eef0f4; --n-200:#dde1e8;
  --n-400:#9aa3b0; --n-600:#5b6472; --n-800:#2b313b; --n-900:#171b22;

  /* roles */
  --text-strong:var(--n-900); --text:var(--n-800); --text-muted:var(--n-600);
  --surface:var(--n-0); --surface-raised:var(--n-0); --surface-sunken:var(--n-50);
  --border:var(--n-200); --border-strong:var(--n-400);
  --accent:#2563eb; --accent-hover:#1d4ed8; --accent-contrast:#ffffff;
  --success:#15803d; --warning:#b45309; --danger:#b91c1c; --info:#2563eb;

  /* spacing (8-base) */
  --s-1:.25rem; --s-2:.5rem; --s-3:.75rem; --s-4:1rem;
  --s-6:1.5rem; --s-8:2rem; --s-12:3rem; --s-16:4rem;

  /* radius + elevation + motion */
  --r-sm:6px; --r-md:10px; --r-lg:16px; --r-full:9999px;
  --shadow-sm:0 1px 2px rgba(0,0,0,.06);
  --shadow-md:0 2px 4px rgba(0,0,0,.06),0 4px 12px rgba(0,0,0,.08);
  --ease-out:cubic-bezier(.2,.8,.2,1); --dur:200ms;
}
```
(Replace the accent + neutral temperature to match the brand. Fix any placeholder values for your stack.)
