---
name: frontend-engineering
description: Build production-quality front-end interfaces in code — semantic HTML, modern CSS, JavaScript and TypeScript, React and component architecture, state, data fetching, responsive layout, animation, and performance. Use whenever writing, structuring, debugging, refactoring, or reviewing front-end code, components, styling, or web UI implementation, even when the user only says build a page, a component, or a feature.
---

# Front-End Engineering

Ship interfaces that are correct, fast, accessible, and maintainable. This skill is the **implementation** layer — how to build UI well in code. It pairs with design judgment (what to build and how it should look/feel); when a design or aesthetic skill is also available, use them together: design decides, this executes.

Apply this whenever the work touches front-end code: markup, styling, components, state, interactivity, or web performance — even if the user just says "build X."

## Operating principles

1. **Semantics first.** Use the right HTML element for the job. Native elements come with behavior, accessibility, and keyboard support for free. Reaching for `div` + JS to rebuild a `button`, `a`, `select`, `details`, or `dialog` is almost always a mistake.
2. **The platform is powerful.** Modern CSS and browser APIs do far more than they used to (grid, container queries, `:has()`, `clamp()`, view transitions, dialog, popover). Prefer platform features over libraries and JS when they suffice.
3. **Accessibility is part of "done."** Keyboard operability, focus management, labels, and contrast are requirements, not polish. Build them in from the start; retrofitting is painful.
4. **Performance is a feature.** Users feel jank, layout shift, and slow loads. Budget for Core Web Vitals; don't ship a megabyte of JS to render a page of text.
5. **State is the hard part.** Most front-end bugs are state bugs. Keep state minimal, derive what you can, colocate it where it's used, and make impossible states unrepresentable.
6. **Code for the next reader.** Clear names, small components, obvious data flow, no clever indirection. Delete code; the best change is often net-negative lines.
7. **Match the stack.** Detect and follow the project's existing framework, conventions, and patterns. Don't introduce React into a vanilla project, or a state library into an app that doesn't need one.

## Workflow

1. **Understand the stack and constraints.** Framework (React/Vue/Svelte/vanilla/WordPress blocks/etc.), styling approach (CSS modules/Tailwind/styled/plain), TS or JS, target browsers, SSR/CSR/SSG, existing components and conventions. Follow what's there.
2. **Model the data and state.** What's the source of truth? What's server state vs UI state vs derived? Sketch the component boundaries around the data, not the visuals.
3. **Structure the markup semantically.** Correct elements, heading order, landmarks, labels. Get this right before styling.
4. **Style with a system.** Use design tokens / variables, a spacing and type scale, and responsive rules. Avoid magic numbers and one-off values. See `references/html-css.md`.
5. **Wire behavior.** Minimal, well-placed state; correct effects; accessible interactions; all component states (default/hover/focus/active/disabled/loading/empty/error). See `references/javascript-react.md`.
6. **Make it fast.** Images, code-splitting, avoid unnecessary re-renders and layout shift. See `references/performance.md`.
7. **Production pass.** Accessibility, forms, error/loading/empty states, edge cases, meta/SEO basics. See `references/production.md`.

## Defaults that prevent most problems

- **Mobile-first, responsive by default.** Fluid type/space with `clamp()`; reflow with grid/flex; container queries for component-level responsiveness. Test small, large, long-content, and empty cases.
- **CSS custom properties for tokens.** Color, spacing, type, radius, motion as variables — themeable and consistent. Logical properties (`margin-inline`, `padding-block`) for RTL-readiness.
- **Never remove focus styles without replacing them.** Use `:focus-visible` for a clear keyboard ring.
- **`prefers-reduced-motion`** honored on every animation; animate `transform`/`opacity`, not layout properties.
- **Type-correct inputs and real labels.** Right `type`/`inputmode`/`autocomplete`; `<label>` always; validate on blur and submit; keep entered data on error.
- **Loading, empty, and error states** for anything async. The happy path alone is unfinished work.
- **No layout shift.** Reserve space for images/embeds (`width`/`height` or `aspect-ratio`); avoid inserting content above existing content.
- **TypeScript where available.** Type props, state, and API boundaries; prefer precise types over `any`. Make illegal states unrepresentable.

## React specifics (when in React)

- Function components + hooks. Keep components small and focused; lift state only as high as needed.
- **You probably don't need that `useEffect`.** Effects are for synchronizing with external systems, not for transforming data (derive during render) or responding to events (do it in the handler). Unnecessary effects cause most React bugs.
- **Keys must be stable and unique** (not array index for dynamic lists).
- **Separate server state from UI state.** Use a data-fetching library (e.g. TanStack Query) or the framework's loader/Server Components for server data; local `useState`/`useReducer` for UI.
- Memoize (`useMemo`/`useCallback`/`memo`) only to fix a measured problem, not preemptively.
- Prefer controlled inputs with a single source of truth; avoid syncing state with effects.
- Respect Rules of Hooks; co-locate logic in custom hooks when it's reused or complex.

## Code quality

- Small, single-purpose components and functions; clear prop interfaces; no prop drilling more than a level or two (use context or composition).
- Consistent naming and file structure matching the project.
- Handle the unhappy paths: null/empty data, slow networks, failures, long strings, many items.
- No dead code, no commented-out blocks, no console noise in shipped code.

## Reference files

Load the relevant file when going deep:

- `references/html-css.md` — semantic HTML and modern CSS: layout (grid/flex), custom properties, container queries, `:has()`, fluid sizing, responsive patterns, animation. Read for markup and styling work.
- `references/javascript-react.md` — JS/TS patterns and React: components, hooks, state, data fetching, effects, events, common pitfalls. Read for behavior and component logic.
- `references/performance.md` — Core Web Vitals (LCP/INP/CLS), loading strategy, images, bundles, rendering performance. Read when speed or smoothness matters.
- `references/production.md` — accessibility implementation, forms, error/loading/empty states, testing, SEO/meta, shipping checklist. Read before calling a feature done.
