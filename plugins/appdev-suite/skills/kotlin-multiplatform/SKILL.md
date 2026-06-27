---
name: kotlin-multiplatform
description: Build professional Kotlin Multiplatform (KMP) apps for iOS and Android — shared architecture, expect/actual, Ktor, SQLDelight, coroutines and Flow, Koin DI, Compose Multiplatform vs native SwiftUI and Jetpack Compose, Kotlin/Native iOS interop with SKIE, Gradle setup, testing, and App Store and Play distribution. Use whenever writing, designing, reviewing, or debugging KMP, Kotlin/Native, shared mobile code, or cross-platform iOS and Android features, even when the user only says build a mobile feature or share this logic.
---

# Kotlin Multiplatform (KMP)

Ship apps that feel **genuinely native on both iOS and Android** while sharing the logic that has no business being written twice. Operate at staff level: the value of KMP is *correctly drawn boundaries* — share the domain, data, and business logic; never compromise the platform experience to force-share UI.

Apply this whenever the work touches KMP, Kotlin/Native, shared mobile modules, or cross-platform iOS/Android features — even when the user just says "build this feature" or "share this logic."

## The core decision: what to share

KMP is not "write once, run anywhere." It's "share what's identical, specialize what's platform-specific." Draw the line deliberately:

- **Always share:** domain models, business rules, use cases, networking, serialization, persistence, validation, state/presentation logic (ViewModels/stores). This is most of the bug-prone code and most of the value.
- **Decide per project — UI:**
  - **Native UI per platform** (SwiftUI on iOS, Jetpack Compose on Android) over a shared presentation layer → **maximum platform fidelity**, best "professional iOS result," more UI work. The right default when the iOS experience must feel first-class.
  - **Compose Multiplatform** (shared UI, Android + iOS) → much less UI code, excellent on Android, increasingly strong on iOS, but you trade some native feel and must work to match iOS conventions. Great for speed, internal tools, or design-driven UIs that aren't trying to look stock-iOS.
  - Pragmatic middle: share UI with Compose MP for most screens, drop to native for the screens where iOS fidelity matters most.
- **Always platform-specific:** anything touching platform UX conventions, OS capabilities (permissions, biometrics, notifications, maps, camera), and store/distribution.

State the decision explicitly at the start of a project; it shapes everything else.

## Professional results on both platforms (the bar)

- **Respect each platform's conventions.** iOS navigation, gestures, haptics, system fonts (SF), and HIG; Android Material 3, back behavior, and predictive back. A shared app that ignores either feels cheap.
- **Native-quality performance:** never block the main thread; correct dispatchers; smooth lists; fast cold start. KMP shared code runs natively on both — there's no excuse for jank.
- **Accessibility on both:** VoiceOver/Dynamic Type on iOS, TalkBack/scaling on Android.
- **Theming per platform** where it matters (Material on Android, system-consistent on iOS).
- The user shouldn't be able to tell the app shares code. That's the goal.

## Workflow

1. **Set boundaries.** Decide shared vs platform-specific, and the UI strategy (native-per-platform vs Compose MP). Define the shared module's public API surface that each platform app consumes.
2. **Structure the shared module** in layers (domain → data → presentation) with `expect`/`actual` only where a platform capability genuinely differs. See `references/architecture.md`.
3. **Pick the shared stack** — Ktor, kotlinx.serialization, SQLDelight, Koin, coroutines/Flow — and wire DI so each platform initializes it cleanly. See `references/shared-stack.md`.
4. **Build the UI** per the chosen strategy, consuming shared state/use cases. See `references/ui-compose-native.md`.
5. **Get iOS interop right** — this is where KMP projects struggle: framework export, suspend/Flow → Swift, memory, threading. Use **SKIE**. See `references/ios-interop.md`.
6. **Set up build, tests, and distribution** — Gradle KMP config, common + platform tests, CI, and shipping to both stores. See `references/build-testing-ship.md`.

## Defaults that keep KMP projects healthy

- **Modern Kotlin/Native memory model** (default since 1.7.20) — no manual freezing; coroutines work across threads. If you see `freeze`/`InvalidMutabilityException` advice, it's outdated.
- **expect/actual sparingly.** Prefer interfaces in `commonMain` with platform implementations injected via DI over scattering `expect`/`actual`. Reserve `expect`/`actual` for genuine platform primitives.
- **Coroutines + Flow as the shared async/reactive backbone;** `StateFlow` for state. Bridge to Swift with SKIE (suspend → `async`, `Flow` → `AsyncSequence`).
- **Keep the shared public API Swift-friendly** — Kotlin generics, sealed hierarchies, and default args don't all map cleanly to Objective-C/Swift. Design the exported surface, don't just expose internals.
- **One source of truth for models** (kotlinx.serialization data classes); never redefine DTOs per platform.
- **Dependency injection** (Koin) so platform implementations and ViewModels are wired consistently; expose a clean entry point to each app.
- **Test the shared module in `commonTest`** — it's the biggest payoff of KMP. Use Turbine for Flow.
- **Pin and align versions** (Kotlin, Compose MP, coroutines, Ktor, SQLDelight, AGP, Xcode). KMP is version-sensitive; mismatches cause cryptic failures.

## Reference files

Load the relevant file when going deep:

- `references/architecture.md` — shared-module layering, expect/actual, clean boundaries, presentation logic sharing, navigation. Read when structuring a KMP project.
- `references/shared-stack.md` — Ktor, kotlinx.serialization, SQLDelight, Koin, coroutines/Flow, settings, the standard shared libraries. Read when building shared data/logic.
- `references/ui-compose-native.md` — Compose Multiplatform vs native SwiftUI/Compose, when to use which, and how to consume shared state for a native feel. Read for UI work.
- `references/ios-interop.md` — Kotlin/Native iOS interop: framework export, SKIE, suspend/Flow to Swift, memory & threading, CocoaPods/SPM, common pitfalls. Read for anything iOS-side.
- `references/build-testing-ship.md` — Gradle KMP setup, targets, XCFramework, testing (common + platform), CI, and App Store/Play distribution. Read for build and release.
