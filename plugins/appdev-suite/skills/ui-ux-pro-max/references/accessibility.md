# Accessibility (WCAG 2.2 AA — practical)

Accessibility is a requirement, not a feature. It also improves usability for everyone. Run this pass before calling any UI done. Most of AA is achievable with a few disciplined defaults.

## Contents
- Color & contrast
- Keyboard & focus
- Targets & spacing
- Text & zoom
- Motion
- Semantics & ARIA
- Forms
- Media
- Quick audit

---

## Color & contrast

- **Text contrast:** ≥ 4.5:1 normal text; ≥ 3:1 large text (≥ 24px, or ≥ 19px bold) against its actual background.
- **Non-text contrast:** ≥ 3:1 for UI component boundaries, icons, focus indicators, and chart/graph elements that convey meaning.
- **Never rely on color alone** to convey state or meaning (error, required, selected, series). Add an icon, text, pattern, or shape.
- Re-check muted gray-on-light and text-over-images — the usual failures. Add a scrim/overlay behind text on photos.

## Keyboard & focus

- **Everything operable by keyboard**, in a logical tab order matching visual order.
- **Visible focus indicator** on every focusable element — high-contrast, ≥ 2px, not clipped. If you remove the default `outline`, replace it (e.g. `:focus-visible` ring). Removing focus with no replacement is the most common a11y failure.
- No keyboard traps; provide a "skip to content" link before long nav.
- Manage focus on route/modal changes (move focus into the dialog, return it on close; trap focus *within* an open modal).
- Standard keys work: Enter/Space activate, Esc closes, arrows move within composite widgets.

## Targets & spacing

- Touch targets ≥ **44×44px** (24×24 minimum per 2.2, but 44 is the comfortable standard). Space targets so they aren't mis-tapped.
- Don't place the only copy of an action in a hover-only control on touch devices.

## Text & zoom

- Support **200% zoom** and browser text resizing without loss of content or function; avoid fixed pixel heights that clip text.
- Use relative units (rem/em) for type; let the page reflow.
- Don't disable user zoom (`maximum-scale=1` / `user-scalable=no` are anti-patterns).
- Keep line length and spacing readable (see typography).

## Motion

- Honor `prefers-reduced-motion: reduce` — remove or shorten non-essential animation, disable autoplay/parallax.
- No content that flashes more than 3×/second (seizure risk).
- Auto-advancing carousels need pause/stop controls.

## Semantics & ARIA

- **Use native HTML first** — `<button>`, `<a>`, `<label>`, `<nav>`, `<main>`, `<h1–h6>`, lists, real form controls. Native elements are accessible by default; re-implementing them with `<div>` + ARIA is error-prone.
- One `<h1>` per page; don't skip heading levels — headings are the screen-reader outline.
- ARIA only to fill genuine gaps ("No ARIA is better than bad ARIA"). Use `aria-label`/`aria-labelledby` for icon-only controls, `aria-live` for async status, `aria-expanded`/`aria-controls` for disclosures.
- Landmarks (`header/nav/main/footer`) for navigation. Decorative images `alt=""`; meaningful images get descriptive alt.

## Forms

- Every input has a programmatically associated `<label>` (not placeholder-only).
- Group related controls with `<fieldset>`/`<legend>`.
- Errors: associate the message with the field (`aria-describedby`), set `aria-invalid`, and move focus to the first error on submit.
- Indicate required state in text/aria, not color alone.

## Media

- Images: meaningful `alt`; decorative `alt=""`.
- Video: captions; audio: transcript. Don't autoplay sound.
- Icon-only buttons: accessible name via `aria-label`.

## Quick audit (run every time)

1. Tab through the whole screen — is focus always visible and order logical?
2. Operate every control with keyboard only (incl. modals, menus, custom widgets).
3. Check contrast on text, icons, borders, and focus rings.
4. Is any meaning carried by color alone?
5. Zoom to 200% — anything clipped or lost?
6. Toggle reduced-motion — does heavy motion stop?
7. Screen-reader spot-check: headings, labels, image alts, live regions.
8. Touch targets ≥ 44px and well-spaced?
