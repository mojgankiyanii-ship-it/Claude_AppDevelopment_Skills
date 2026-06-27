# UI — Compose Multiplatform vs Native

The biggest fidelity decision. There's no universally right answer — choose per project against the bar of how native iOS must feel.

## Contents
- The decision
- Native UI per platform
- Compose Multiplatform
- Consuming shared state
- Platform fidelity checklist

---

## The decision

| | Native per platform | Compose Multiplatform |
|---|---|---|
| iOS feel | Best — true SwiftUI/UIKit | Good and improving; needs effort to match HIG |
| Android feel | Native Compose | Native Compose (same engine) |
| UI code shared | 0% (logic still shared) | ~90% UI shared |
| Speed to build | Slower (two UIs) | Faster (one UI) |
| Best for | iOS-first or premium consumer apps where platform feel is the product | Speed, internal/B2B tools, brand-driven UIs, smaller teams |

**Recommended default for a "professional iOS result":** share everything below the UI (domain, data, presentation/state) and build **native UI per platform** — SwiftUI on iOS, Jetpack Compose on Android. You still delete most duplicate logic, and each platform looks and feels first-class.

**Pragmatic hybrid:** Compose MP for the bulk of screens, native for the few where iOS fidelity matters most (onboarding, key flows, anything gesture/animation-heavy). Compose MP can embed native views and be embedded in native screens.

Decide explicitly and early — it sets the project structure.

## Native UI per platform

- **iOS:** SwiftUI (or UIKit) consuming the shared module as a framework. Observe shared `StateFlow` via SKIE; call shared use cases. Build with `NavigationStack`, native components, SF Symbols, system fonts, haptics, and HIG patterns.
- **Android:** Jetpack Compose + Material 3, collecting shared `StateFlow`, using predictive back, dynamic color, and platform navigation.
- The shared presentation layer keeps both platforms behaviorally identical while each renders natively. This is the cleanest path to "doesn't look cross-platform."

## Compose Multiplatform

- One Compose UI in `commonMain` runs on Android (native) and iOS (rendered via Skia). Mature on Android; production-capable on iOS in recent releases — confirm the current stability status and known gaps for your version before committing.
- **To feel native on iOS with Compose MP:** adopt iOS-appropriate components and motion, use the Cupertino/adaptive widgets where available, match navigation gestures (swipe-back), respect safe areas, and handle text/scroll behavior carefully. Test on real devices — the gap is in the details (scroll physics, keyboard, native dialogs).
- Use `UIKitView`/interop to embed truly native pieces (maps, web views, camera, native pickers) inside Compose MP.
- Great fit when the design is custom/branded rather than trying to look stock-iOS, or when speed and a small team outweigh maximum platform fidelity.

## Consuming shared state

- Expose `StateFlow<UiState>` + intent functions from a shared ViewModel/store.
- **Android (Compose):** `val state by viewModel.state.collectAsStateWithLifecycle()`.
- **iOS (SwiftUI):** with **SKIE**, observe the `Flow` as an `AsyncSequence` (or a published wrapper) and drive an `@Observable`/`ObservableObject` so views update idiomatically. Without SKIE, you wrap the Flow manually with a collector + completion handler.
- Keep UI declarative and stateless: render state, send intents. No business logic in the view layer on either platform.

## Platform fidelity checklist (the "professional" bar)

- **iOS:** native navigation + swipe-back, SF system font + Dynamic Type, SF Symbols, native haptics, sheets/alerts/menus that match iOS, safe-area correctness, VoiceOver labels, light/dark + system settings respected.
- **Android:** Material 3 components, predictive back, dynamic color (Material You) where appropriate, correct insets/edge-to-edge, TalkBack labels, theming + dark mode.
- **Both:** smooth 60/120fps scrolling, no main-thread jank, fast cold start, sensible loading/empty/error states, large-text and accessibility support, localized strings and formats.
- The test: hand the iOS build to an iOS user and the Android build to an Android user — neither should sense it's cross-platform.
