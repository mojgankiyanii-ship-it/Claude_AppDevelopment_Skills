# HTML & CSS

Semantic markup and modern CSS. Most "front-end is hard" pain disappears when you use the right element and let modern CSS do the layout.

## Contents
- Semantic HTML
- CSS architecture & tokens
- Layout (grid & flex)
- Responsive & fluid
- Modern CSS features
- Animation

---

## Semantic HTML

- Use the element that *means* the thing: `button` for actions, `a` for navigation, `nav/header/main/footer/aside/section/article` for structure, `ul/ol/li` for lists, `table` for tabular data, `form/label/input/fieldset/legend` for forms, `details/summary` for disclosure, `dialog` for modals.
- One `h1` per page; don't skip heading levels — headings are the document outline and the screen-reader map.
- Landmarks (`header`, `nav`, `main`, `footer`) let assistive tech jump around. Exactly one `main`.
- Native interactive elements come with focus, keyboard, and roles for free. Rebuilding them with `div` + handlers means re-implementing all of that (and usually doing it incompletely).
- Images: meaningful `alt`, or `alt=""` if decorative. Always set `width`/`height` (or `aspect-ratio`) to prevent layout shift. Use `loading="lazy"` below the fold, and responsive `srcset`/`sizes` or `<picture>`.
- Use `<button type="button">` inside forms unless it should submit.

## CSS architecture & tokens

- **Custom properties for tokens.** Define color, spacing, type, radius, shadow, motion once on `:root`; reference everywhere. Themeable (light/dark via overrides), consistent, no magic numbers.
- **A scale, not arbitrary values.** Spacing on a 4/8 base; type on a modular scale. One-off pixel values are the main source of inconsistency.
- **Keep specificity low and flat.** Prefer single classes; avoid deep descendant chains and `!important`. Modern resets + a small utility/token layer scale better than deeply nested rules.
- **Logical properties** (`margin-inline`, `padding-block`, `inset`, `border-start-start-radius`) for writing-mode/RTL resilience.
- **Scope styles** with CSS Modules, a methodology (BEM), Tailwind, or `@scope` — match the project. Avoid leaking global selectors.
- Use a modern reset (box-sizing: border-box; sensible defaults) once, globally.

## Layout (grid & flex)

- **Flexbox** for one-dimensional layout (a row or column that distributes space): toolbars, button groups, nav.
- **Grid** for two-dimensional layout and page structure: `grid-template-columns`, `gap`, `grid-template-areas`. `repeat(auto-fit, minmax(min, 1fr))` gives responsive card grids with no media queries.
- `gap` instead of margins for spacing between flex/grid children.
- Center reliably: `display:grid; place-items:center;` or flex with `align/justify`.
- Avoid fixed heights that clip content; let content size the box. Use `min-height` over `height` when in doubt.

## Responsive & fluid

- **Mobile-first:** base styles for small screens, `min-width` media queries to add complexity as space grows.
- **Fluid sizing with `clamp(min, preferred, max)`** for type and spacing — smooth scaling between breakpoints instead of stepped jumps.
- **Container queries** (`container-type`, `@container`) for components that should respond to *their own* width, not the viewport — the right tool for reusable cards/sidebars.
- Breakpoints follow content, not devices. Common: ~480, 768, 1024, 1280.
- Constrain line length (`max-width: 65ch`) for readable text.
- Respect safe areas on mobile (`env(safe-area-inset-*)`) for full-bleed layouts.

## Modern CSS features (use them)

- **`:has()`** — style a parent based on its children (e.g. a card that has an image), or siblings. Removes a lot of old JS.
- **`:focus-visible`** — keyboard-only focus ring; pair with `:focus-within` for groups.
- **`aspect-ratio`** — reserve media space, no shift.
- **`clamp()` / `min()` / `max()`** — fluid, capped sizing without media queries.
- **`gap`** in flex and grid.
- **`color-mix()`** and relative color syntax for deriving tints/shades from a base token.
- **`dialog`** element + `::backdrop` for accessible modals; **popover** attribute for popovers/menus.
- **View Transitions API** for smooth state/page transitions where supported (progressive enhancement).
- **`accent-color`** to theme native checkboxes/radios/range.
- **`scroll-snap`** for carousels; **`position: sticky`** for headers.

## Animation

- Animate **`transform` and `opacity`** — they're GPU-friendly and don't trigger layout. Avoid animating `width/height/top/left` (janky); use `transform` or the FLIP technique.
- Timing: 150–250ms for UI, ease-out for entrances. Keep it subtle and purposeful.
- **Always** gate non-essential motion behind `@media (prefers-reduced-motion: reduce)`.
- Prefer CSS transitions/animations for simple cases; reach for JS (Web Animations API, a library) only for complex sequencing or physics.
- `will-change` sparingly and only when a real perf issue is measured.
