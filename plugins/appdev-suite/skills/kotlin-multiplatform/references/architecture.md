# KMP Architecture

Draw boundaries well and the rest of KMP is easy. Share the logic; specialize the platform edges; keep the exported API clean.

## Contents
- Source sets & module layout
- Layering
- expect/actual (and when not to)
- Sharing presentation logic
- Navigation
- Boundaries & API surface

---

## Source sets & module layout

- **`commonMain`** — all shared code (domain, data, presentation). The bulk of the app.
- **`androidMain` / `iosMain`** — platform implementations of `expect` declarations and platform-only code. (`iosMain` typically aggregates `iosX64`/`iosArm64`/`iosSimulatorArm64`.)
- **`commonTest`** + platform test source sets.
- A common layout: a single `shared` module, or multiple feature/layer modules (`:domain`, `:data`, `:feature-x`) for larger apps. Modularize when the app grows; don't over-split early.
- The Android app (`androidApp`) and iOS app (`iosApp`, an Xcode project) consume the shared module as a dependency / framework.

## Layering

Keep a clean, dependency-inward structure inside `commonMain`:

- **Domain** — pure Kotlin: models, value objects, use cases/interactors, repository *interfaces*, business rules. No framework or platform dependencies. Most testable, most valuable to share.
- **Data** — repository implementations, networking (Ktor), persistence (SQLDelight), DTOs + mappers, caching. Implements domain interfaces.
- **Presentation** — shared ViewModels/stores exposing `StateFlow<UiState>` and handling intents/events. Platform UI observes this. (Optional to share — see below.)

Dependencies point inward (presentation → domain ← data). The platform UI and platform capabilities sit outside.

## expect/actual (and when not to)

- `expect`/`actual` declares a common API with platform-specific implementations (e.g. `expect fun platformName(): String`).
- **Prefer dependency injection over `expect`/`actual`** for most platform behavior: define an interface in `commonMain`, implement it in `androidMain`/`iosMain`, and inject it. This is more flexible, testable, and avoids littering the codebase with `expect`/`actual` pairs.
- Reserve `expect`/`actual` for true platform primitives where an interface is overkill (platform constants, a thin wrapper over a system API).
- Common platform-specific needs: secure storage, biometrics, permissions, notifications, file paths, system locale/timezone, logging, UUID/random, current time — wrap each behind a `commonMain` interface.

## Sharing presentation logic

- A shared presentation layer (ViewModel/store with `StateFlow`) lets both platforms consume identical state and logic — high payoff, keeps platforms consistent.
- Patterns: a plain shared class exposing `StateFlow` + intent functions; or libraries like **Decompose** (lifecycle-aware components + navigation) or **MVIKotlin** for a structured MVI store.
- On Android: collect the `StateFlow` in Compose. On iOS: observe via SKIE (`Flow` → `AsyncSequence`/published) so SwiftUI updates naturally.
- Keep platform UI dumb: it renders state and sends intents. No business logic in SwiftUI/Compose.
- If you share UI with Compose MP, the shared ViewModel is consumed directly in shared Compose.

## Navigation

- **Native UI per platform:** use each platform's native navigation (SwiftUI `NavigationStack`, Android Navigation/Compose Navigation). Optionally drive it from a shared navigation state.
- **Shared UI (Compose MP):** use a multiplatform nav library — **Decompose** (recommended for robust, lifecycle-correct navigation), Voyager, or Compose Navigation's multiplatform support.
- Keep navigation intent in shared state where it helps consistency, but don't fight the platform's native transitions/gestures when you want a native feel.

## Boundaries & API surface

- **Design the shared module's public API for its consumers**, especially Swift. The exported surface is a product, not an accident — keep it small, stable, and idiomatic.
- Things that don't bridge cleanly to Swift/Obj-C: Kotlin generics (erased/limited), sealed classes (flattened — SKIE fixes this with exhaustive Swift enums), default arguments (not exposed), `suspend`/`Flow` (need SKIE or manual wrappers), overloads, and inline/value classes. Plan the public API around these limits (see ios-interop reference).
- Expose use cases and observable state, not internal data-layer types. Map DTOs to domain models inside the shared module.
- Provide a clean initialization entry point (e.g. a `KoinInitializer` / `initKoin()`), so each app starts the shared stack with one call.
