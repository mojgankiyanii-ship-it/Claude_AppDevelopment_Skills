# Performance

Performance is UX. Users feel slow loads, jank, and layout shift even when they can't name them. Optimize what's measured — but these defaults prevent most problems for free.

## Contents
- Core Web Vitals
- Loading strategy
- Images & media
- JavaScript & bundles
- Rendering performance
- Measuring

---

## Core Web Vitals (the targets)

- **LCP (Largest Contentful Paint)** — load speed of the main content. Target **< 2.5s**. Usually the hero image or heading.
- **INP (Interaction to Next Paint)** — responsiveness to input (replaced FID in 2024). Target **< 200ms**. Driven by main-thread work blocking interactions.
- **CLS (Cumulative Layout Shift)** — visual stability. Target **< 0.1**. Caused by un-sized media, injected content, and late-loading fonts.

Optimize these three first; they're what users feel and what search ranks.

## Loading strategy

- **Critical path first.** Get meaningful content rendered fast; defer the rest.
- **Lazy-load below-the-fold** images (`loading="lazy"`) and code (dynamic `import()` / route-level code splitting).
- **Preload** truly critical resources (hero image, key font) with `<link rel="preload">`; **preconnect** to required origins.
- **Fonts:** `font-display: swap` (or `optional`), preload the main weight, subset, and self-host where possible to cut a render-blocking round trip. Provide a matching fallback to reduce shift.
- **Eager-load only the hero/LCP image**; everything else lazy.
- Prefer SSR/SSG for fast first paint where the stack supports it; hydrate or stream progressively.

## Images & media

- **Right format:** AVIF/WebP over JPEG/PNG; SVG for icons/line art.
- **Responsive images:** `srcset` + `sizes` (or `<picture>`) so devices download an appropriately sized file.
- **Always set dimensions** (`width`/`height` or `aspect-ratio`) — single biggest CLS fix.
- Compress; don't ship 4000px images into 400px slots.
- Use a CMS/image CDN for automatic resizing/format when available.
- Video: `preload="none"` or a poster; don't autoplay heavy video on mobile.

## JavaScript & bundles

- **Ship less JS.** It's the most expensive resource — parse/compile/execute blocks the main thread (hurts INP). A page of text shouldn't need a megabyte of script.
- **Code-split** by route and lazy-load heavy components (charts, editors, maps) on demand.
- **Tree-shake**; import only what you use (`import { x } from 'lib'`, avoid whole-library imports like all of lodash).
- Audit dependencies — a small feature pulling a huge lib is a smell; prefer the platform or a lighter alternative.
- Defer non-critical third-party scripts (analytics, chat) — they're common INP/LCP killers; load them `async`/`defer` or after interaction.

## Rendering performance

- **Avoid layout thrash:** batch DOM reads then writes; don't read layout (`offsetHeight`) in a loop with writes.
- **Animate `transform`/`opacity`** only; they skip layout/paint. Avoid animating layout properties.
- **Virtualize long lists** (windowing) so you render only what's visible.
- **React:** prevent needless re-renders — stable props/keys, split components, memoize *measured* hot paths, keep context lean. Don't optimize blindly; profile first.
- Use `content-visibility: auto` to skip rendering offscreen sections cheaply.
- Debounce/throttle scroll/resize/input handlers; use `IntersectionObserver` instead of scroll math.

## Measuring (before optimizing)

- **Lighthouse** / PageSpeed Insights for an overall picture and CWV.
- **Chrome DevTools Performance** panel for main-thread/jank; **Coverage** for unused JS/CSS.
- **React DevTools Profiler** for re-render hotspots.
- **Field data** (real users) beats lab data — use the CrUX/Web Vitals from real sessions when available.
- Optimize the biggest measured cost first; don't micro-optimize what the profiler says is cheap.
