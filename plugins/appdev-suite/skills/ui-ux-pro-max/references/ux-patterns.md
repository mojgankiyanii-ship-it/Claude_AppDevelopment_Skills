# UX Patterns

Interaction and flow decisions. Good visual design on a confusing flow still fails.

## Contents
- Navigation
- Forms & inputs
- Feedback & system status
- Component states
- Data display
- Microcopy

---

## Navigation

- Make the user's location obvious (active states, breadcrumbs for depth).
- Keep primary navigation stable and shallow; aim for ≤ 7 top-level items.
- The most important destination/action gets the most prominent treatment (one primary CTA).
- Don't hide core navigation behind a hamburger on desktop if space allows.
- Provide a way back/out of every state (cancel, close, back). Never trap the user.
- Predictable placement: logo top-left → home; primary actions top-right or bottom on mobile.

## Forms & inputs

Forms are where products are won or lost. Defaults:

- **One column.** Multi-column forms cause skipping and errors. Group related fields.
- **Labels above fields**, always visible. Placeholders are not labels (they vanish on type and fail accessibility).
- **Ask the minimum.** Every field has a cost; cut optional ones or defer them.
- **Right input for the data:** correct `type`/`inputmode`, native pickers, sensible defaults, autocomplete tokens.
- **Inline validation** on blur, not only on submit. Show success and error states clearly.
- **Errors are specific and kind:** say what's wrong *and* how to fix it ("Password needs 8+ characters", not "Invalid input"). Put the message next to the field and keep entered data.
- **Primary action is obvious and labeled by outcome** ("Create account"). Secondary/cancel is visually quieter.
- **Disabled submit is hostile** if the user can't tell why — prefer enabling it and validating on click, or clearly indicate what's missing.
- Show progress for multi-step; let users go back without losing data.

## Feedback & system status

- Every action gets a response within ~100ms (visual acknowledgement), even if the result is still loading.
- **Loading:** skeletons for content, spinners only for short waits, progress bars for long/known durations. Preserve layout to avoid shift.
- **Success:** confirm consequential actions (toast, inline, or state change). Don't over-confirm trivial ones.
- **Errors:** explain + offer recovery. Never blame the user. Never show raw error codes alone.
- **Destructive actions:** confirm, and prefer undo over a scary modal where feasible.
- **Optimistic UI** for fast-feeling apps, with graceful rollback on failure.

## Component states (design all of them)

For each interactive element: **default · hover · focus-visible · active/pressed · disabled · loading · selected · error**. Missing `focus-visible` breaks keyboard use — never remove focus styling without replacing it.

For each content container: **empty · loading (skeleton) · partial · error · ideal/full**. The empty and error states are where products feel unfinished — design them deliberately:
- **Empty state:** explain what goes here, why it's empty, and the next action (often the primary CTA's best home).
- **Error state:** what happened, reassurance, and a retry/route out.

## Data display

- **Tables** for comparison across many rows; **cards** for scannable, image-led items; **lists** for linear reading.
- Tables: right-align numbers, tabular figures, sticky header, clear sort affordance, zebra or row hover for tracking, sensible density.
- Don't truncate critical data without a way to see the full value.
- **Charts:** pick the type for the question (trend → line; comparison → bar; part-to-whole → stacked/treemap, rarely pie). Label directly; minimize legends, gridlines, and chartjunk; never rely on color alone.
- Show units, precision, and empty/zero states honestly.

## Microcopy

Words are interface. Principles:
- **Action by outcome:** button text = what happens ("Send invite", "Delete project"), not "OK/Submit".
- **Specific errors:** what + why + fix.
- **Helpful empty states:** orient + next step.
- **Plain, human, brief.** Cut filler. Sentence case usually reads friendlier than Title Case for UI.
- **Consistent terms:** the same thing has the same name everywhere (don't alternate "project/workspace/board").
- **Numbers and dates** formatted for the locale; relative time ("2h ago") where it helps.
