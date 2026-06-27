# Production Readiness

The work that separates a demo from something you can ship: accessibility, real forms, the unhappy paths, and the final checklist.

## Contents
- Accessibility in code
- Forms
- States (loading / empty / error)
- Error handling
- SEO & meta
- Testing
- Ship checklist

---

## Accessibility in code

(Build it in; retrofitting is costly.)

- **Native elements first** — `button`, `a`, `label`, `select`, `dialog` give you roles, focus, and keyboard for free.
- **Keyboard operable:** everything reachable and usable by keyboard, in logical tab order. No keyboard traps. Provide a "skip to content" link.
- **Visible focus:** never `outline: none` without a `:focus-visible` replacement.
- **Labels:** every input has an associated `<label>` (or `aria-label` for icon-only controls). Don't rely on placeholder as label.
- **Focus management:** on opening a modal/menu, move focus in and trap it; on close, return focus to the trigger. Use the `dialog` element to get much of this for free.
- **ARIA only to fill gaps** — `aria-live` for async status, `aria-expanded`/`aria-controls` for disclosures, `aria-current` for active nav. No ARIA is better than wrong ARIA.
- **Contrast** ≥ 4.5:1 text, ≥ 3:1 UI/large text. Never convey meaning by color alone.
- **Respect `prefers-reduced-motion`.**
- Test: tab through it, run it with the keyboard only, run an axe/Lighthouse a11y audit.

## Forms

- Real `<form>` with `onSubmit` + `preventDefault`; submit works on Enter.
- Correct `type`, `inputmode`, and `autocomplete` tokens; native pickers where possible.
- Labels above fields; group with `fieldset`/`legend`.
- **Validate on blur and on submit**, not only submit. Show specific errors (what + how to fix) next to the field; set `aria-invalid` and `aria-describedby`; move focus to the first error.
- **Keep entered data on error** — never clear the form.
- Disable/spinner the submit during async submit to prevent double-submit; re-enable on result.
- Single source of truth for each field (controlled), or uncontrolled + `FormData` — don't mix per field.

## States (loading / empty / error)

For every async or dynamic surface, design and implement all of:
- **Loading** — skeletons for content, spinners for short waits; preserve layout (no shift).
- **Empty** — explain what goes here and offer the next action; don't show a blank box.
- **Partial / ideal** — the normal populated state.
- **Error** — what happened, reassurance, and a retry/way out. Never a blank screen or raw stack trace.

Shipping only the happy path is the most common "looks unfinished" failure.

## Error handling

- **Error boundaries** (React) around routes/regions so one failure doesn't blank the app; show a recovery UI.
- Catch and handle async failures; surface a user-friendly message and log the technical detail.
- Guard against null/empty/malformed data at the boundary; fail loudly in dev, gracefully in prod.
- Don't swallow errors silently; don't leak internals to users.

## SEO & meta

- Server-render or pre-render content that needs to be indexed.
- Unique `<title>` and meta description per page; semantic headings; descriptive link text.
- Open Graph / Twitter card tags for shareable pages; canonical URLs.
- `lang` on `<html>`; valid, semantic markup; sitemap/robots where relevant.
- Accessible and SEO-friendly overlap a lot — good semantics serve both.

## Testing

- **Unit** for logic/utilities; **component** tests (Testing Library) that assert behavior from the user's perspective (roles, labels, text) — not implementation details.
- **E2E** (Playwright/Cypress) for critical flows (auth, checkout, submit).
- Test the unhappy paths: empty, error, slow network, long content, many items.
- Include an automated a11y check (axe) in the component/e2e layer.

## Ship checklist

1. Semantics correct; one `h1`; logical heading order; landmarks.
2. Keyboard: full operability, visible focus, no traps, focus managed in overlays.
3. Contrast passes; meaning never by color alone.
4. Responsive from small to large; no horizontal scroll; targets ≥ 44px.
5. Loading, empty, and error states implemented everywhere async.
6. Forms: labels, correct types, validation with specific messages, data preserved on error.
7. No layout shift; images sized; fonts don't cause reflow.
8. JS shipped is reasonable; heavy parts code-split; third-party deferred.
9. Errors handled and bounded; no console noise; no dead code.
10. Lighthouse pass (perf + a11y + best practices); test real devices, not just desktop.
