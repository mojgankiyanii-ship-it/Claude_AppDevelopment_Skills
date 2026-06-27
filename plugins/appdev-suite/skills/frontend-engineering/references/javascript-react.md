# JavaScript / TypeScript & React

Behavior, state, and component logic. Most front-end bugs are state bugs — keep state minimal and data flow obvious.

## Contents
- JS/TS fundamentals
- Component architecture
- State
- Effects (and avoiding them)
- Data fetching
- Events & interaction
- Common React pitfalls

---

## JS/TS fundamentals

- **TypeScript** wherever available. Type props, state, function signatures, and API boundaries. Prefer precise unions and discriminated unions so **illegal states are unrepresentable**. Avoid `any`; use `unknown` + narrowing at boundaries.
- Prefer immutable updates and pure functions for transforming data; keep side effects at the edges.
- Use modern JS: optional chaining `?.`, nullish coalescing `??`, destructuring, array methods (`map/filter/reduce/find`), `structuredClone`, `Intl` for dates/numbers/currency.
- Guard the unhappy paths: null/undefined, empty arrays, failed requests, NaN, long strings.
- Don't reinvent the platform: `URL`, `URLSearchParams`, `fetch`, `FormData`, `Intl`, `crypto.randomUUID()`.

## Component architecture

- **Small, single-purpose components.** If a component does layout + data + 3 behaviors, split it.
- **Composition over configuration.** Prefer `children` and slots over a dozen boolean props. Avoid "god components" with 20 props.
- **Props down, events up.** One source of truth per piece of state; pass data down, notify changes up via callbacks.
- **Separate presentational from container concerns** where it clarifies (a dumb component that renders props vs a hook/parent that owns data).
- Avoid prop-drilling beyond a level or two — use context for genuinely shared/global state (theme, auth, locale), composition otherwise. Context is not a state manager; don't put rapidly-changing state in one big context.

## State

- **Minimize state.** Anything you can *derive* during render, don't store. Duplicated/derived state drifts out of sync — the classic bug.
- **Colocate.** Keep state as close to where it's used as possible; lift only when multiple components truly need it.
- **Categories:** server/remote state (from APIs), URL state (filters, tabs, pagination — put it in the URL so it's shareable and back-button-friendly), and local UI state. Treat them differently.
- `useReducer` over many related `useState` when transitions are complex or interdependent.
- Make impossible states impossible: model `status` as `'idle' | 'loading' | 'success' | 'error'` rather than separate booleans (`isLoading`, `isError`) that can contradict.

## Effects (and avoiding them)

`useEffect` is for **synchronizing with external systems** (DOM nodes you don't control, subscriptions, network in non-framework setups, timers, third-party widgets). It is **not** for:

- **Transforming data for render** → compute it during render (optionally `useMemo` if expensive). Don't mirror props into state via an effect.
- **Responding to a user event** → do it in the event handler, not an effect watching state.
- **Resetting state on prop change** → use a `key` to remount, or compute during render.

Unnecessary effects cause cascading renders, race conditions, and stale data — a top source of React bugs. Each effect needs a correct, complete dependency array and a cleanup function for subscriptions/timers/listeners.

## Data fetching

- **Don't hand-roll fetching-in-`useEffect` for real apps.** Use the framework's data layer (Server Components / route loaders in Next/Remix) or a library like **TanStack Query** / SWR — they handle caching, dedup, retries, loading/error states, and revalidation correctly.
- Always render **loading, empty, and error** states for async data. Handle the request lifecycle explicitly.
- Cancel/ignore stale responses (AbortController, or the library's built-ins) to avoid race conditions.
- Keep server state out of global client state stores; let the data layer be the cache.
- Validate/parse API responses at the boundary (e.g. with a schema) so bad data fails loudly, not deep in the UI.

## Events & interaction

- Attach handlers to the semantic element (`button`, `a`, form `onSubmit`). Don't put click handlers on `div`s without role/tabindex/keyboard support.
- Forms: handle `onSubmit` with `preventDefault`; read with `FormData` or controlled state; keep one source of truth.
- Debounce/throttle expensive handlers (search, scroll, resize); clean them up.
- Keyboard: Enter/Space activate, Esc closes, arrows for composite widgets; manage focus on open/close of menus and dialogs.

## Common React pitfalls

- Array **index as `key`** for dynamic/reorderable lists → use a stable id.
- **Derived state stored in state** → derive during render instead.
- **Mutating state** directly → always produce new objects/arrays.
- **Missing/incorrect effect dependencies** → stale closures and bugs; don't silence the linter, fix the design.
- **Over-memoization** → `useMemo`/`useCallback` everywhere adds complexity; add them to fix a measured re-render problem.
- **Huge contexts** that re-render the tree on every change → split contexts or use a store.
- **Conditional hooks** → never call hooks conditionally or in loops.
