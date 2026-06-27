# Shared Stack

The standard, production-proven multiplatform libraries and how to use them well. All of this lives in `commonMain` and runs natively on both platforms.

## Contents
- Coroutines & Flow
- Networking (Ktor)
- Serialization
- Persistence (SQLDelight)
- Dependency injection (Koin)
- Key-value settings
- Other useful libraries

---

## Coroutines & Flow

- Coroutines are the shared async backbone; they work across threads under the modern memory model (no freezing).
- **`StateFlow`** for observable state, **`SharedFlow`** for events, cold `Flow` for streams. Expose these from shared ViewModels/repositories.
- **Dispatchers:** use `Dispatchers.Default`/`IO` for work, `Dispatchers.Main` for UI updates. `Dispatchers.IO` exists on all targets in recent coroutines versions; otherwise inject a dispatcher provider. Never block the main thread.
- Structured concurrency: scope work to lifecycles; cancel on teardown. Inject a `CoroutineScope` or use the platform's lifecycle scope.
- Bridge to Swift with **SKIE** (`suspend` → `async`, `Flow` → `AsyncSequence`) — see ios-interop. Without SKIE you write manual completion-handler wrappers.

## Networking (Ktor)

- **Ktor Client** is the multiplatform HTTP client. Configure once in `commonMain`; it uses a platform engine (OkHttp on Android, Darwin/NSURLSession on iOS).
- Install plugins: `ContentNegotiation` with `kotlinx.serialization` JSON, `Logging`, `HttpTimeout`, `Auth` (bearer/refresh), and a default request (base URL, headers).
- Centralize the client and API services; return domain models (map from DTOs) not raw responses. Handle errors into a typed result.
- Configure timeouts, retries for idempotent calls, and token refresh in the client, not per call site.

## Serialization

- **kotlinx.serialization** — `@Serializable` data classes as the single source of truth for models across platforms. No per-platform DTOs.
- Configure JSON leniently where needed (`ignoreUnknownKeys = true`, explicit defaults). Use `@SerialName` for wire names.
- Use it for network bodies and any local JSON. Keep DTOs in the data layer; map to domain models.

## Persistence (SQLDelight)

- **SQLDelight** — typed SQL with multiplatform drivers (Android, native iOS). You write `.sq` schema/queries; it generates type-safe Kotlin APIs. Real relational DB on both platforms.
- Use the platform driver via DI (`AndroidSqliteDriver`, `NativeSqliteDriver`). Run queries off the main thread; expose results as `Flow` for reactive UI.
- Handle migrations explicitly via versioned `.sq` migrations. Back up nothing sensitive unencrypted.
- For simple needs, **multiplatform-settings** (below) or a key-value store may be enough; reach for SQLDelight when you have real relational/queryable data.

## Dependency injection (Koin)

- **Koin** is the common multiplatform DI choice (kotlin-inject is a compile-time alternative). Define modules in `commonMain`; provide platform implementations from `androidMain`/`iosMain`.
- Expose an `initKoin()` entry point that each app calls at startup (Android `Application`, iOS app init). Provide a helper for iOS so Swift can start Koin and resolve dependencies.
- Inject interfaces (repositories, platform services, dispatchers) — this is how you keep `expect`/`actual` out of the codebase and keep things testable.

## Key-value settings

- **multiplatform-settings** wraps `SharedPreferences`/`NSUserDefaults` (and can back onto encrypted/Keychain stores) for simple key-value config and flags. Good for preferences, tokens (prefer secure storage), and lightweight state.

## Other useful libraries

- **kotlinx-datetime** — multiplatform dates/times (don't hand-roll).
- **Turbine** — testing `Flow` emissions in `commonTest`.
- **Napier / Kermit** — multiplatform logging.
- **Decompose / MVIKotlin / Voyager** — shared navigation and state architecture.
- **Coil 3 / Kamel** — image loading for Compose Multiplatform.
- **Firebase via GitLive** or KMP-friendly wrappers — when you need Firebase across platforms.
- Prefer well-maintained, multiplatform-first libraries; check that a dependency actually supports the iOS target before adopting it.
