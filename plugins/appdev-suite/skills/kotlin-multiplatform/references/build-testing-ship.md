# Build, Testing & Shipping

Gradle setup, targets, testing across platforms, CI, and getting the app into both stores. KMP is version-sensitive — align and pin everything.

## Contents
- Gradle & targets
- Building the iOS framework
- Testing
- CI/CD
- Distribution (App Store & Play)
- Versioning & dependency hygiene

---

## Gradle & targets

- Use the **Kotlin Multiplatform Gradle plugin** with the Kotlin DSL (`build.gradle.kts`) and a **version catalog** (`libs.versions.toml`) so Kotlin, Compose MP, coroutines, Ktor, SQLDelight, AGP, and plugin versions stay aligned in one place.
- Declare targets in the shared module:
  - **`androidTarget()`** for Android.
  - **iOS:** `iosX64()` (Intel sim), `iosArm64()` (device), `iosSimulatorArm64()` (Apple-silicon sim). Configure the framework output (often a `binaries.framework { baseName = "Shared" }` or via the CocoaPods/SPM integration).
- Organize source sets: `commonMain`/`commonTest`, `androidMain`/`androidUnitTest`, `iosMain`/`iosTest` (with the three iOS targets sharing an `iosMain`).
- Keep dependencies in the right source set — common libs in `commonMain`, platform engines/drivers in platform source sets.

## Building the iOS framework

- Produce an **XCFramework** for the iOS app to consume (covers device + simulator + arch).
- Integration: **direct SPM export** (lighter, increasingly preferred — verify your Kotlin version supports it), the **Kotlin CocoaPods plugin** (mature, `pod install` flow), or a locally referenced XCFramework.
- Wire the framework build into the iOS build so Swift never compiles against a stale API after Kotlin changes.
- Keep **Kotlin and Xcode versions compatible**; mismatches are a top cause of opaque native build failures.

## Testing

The shared module is the biggest testing payoff — write most tests once in `commonMain`.

- **`commonTest`** with **kotlin.test** for domain/use-case/repository logic — runs on all targets.
- **Turbine** for testing `Flow`/`StateFlow` emissions.
- **Mock** dependencies behind the interfaces you defined (manual fakes are often cleanest in KMP; mocking libraries with multiplatform support exist but fakes avoid platform gaps).
- **Platform tests** (`androidUnitTest`, `iosTest`) for `actual` implementations and platform integration.
- **UI tests** stay per platform: SwiftUI/XCUITest on iOS, Compose UI tests/Espresso on Android. (Compose MP has its own UI test APIs if you share UI.)
- Run `commonTest` on both the JVM/Android and an iOS simulator target in CI to catch platform-specific surprises (e.g. a JVM-only assumption).

## CI/CD

- **Build matrix:** Android build + tests on a Linux runner; iOS framework build + tests and the Xcode app build on a **macOS runner** (required for Apple toolchain).
- Cache Gradle and Kotlin/Native dependencies; the first Kotlin/Native build is slow (downloads the toolchain).
- Pipeline: lint/format (ktlint/detekt) → shared tests → Android assemble → iOS framework + app build → distribute. Sign builds in CI with securely stored credentials (not in the repo).
- Gate merges on shared tests; produce signed artifacts for distribution.

## Distribution (App Store & Play)

- **Android:** build an **App Bundle (.aab)**, sign it, upload to **Play Console**; use internal/closed/open testing tracks before production; staged rollout.
- **iOS:** archive the Xcode app, sign with the right provisioning/certificates, upload to **App Store Connect**, distribute via **TestFlight**, then submit for review. Mind App Review guidelines.
- Automate signing and upload (Fastlane is common on both platforms; or each store's CLI/API). Keep certificates/keystores in secure CI secrets.
- The shared KMP framework is an implementation detail to the stores — you ship two normal native apps; nothing special is required because code is shared.

## Versioning & dependency hygiene

- **Pin and align** Kotlin, Compose Multiplatform, coroutines, Ktor, SQLDelight, SKIE, AGP, and the Android/iOS toolchains. KMP breaks loudly on mismatches; a version catalog keeps it sane.
- Upgrade Kotlin and Compose MP together and read their compatibility notes — Compose MP and SKIE track specific Kotlin versions.
- Before adopting any library, confirm it actually supports the iOS target (many Android libs don't). Prefer multiplatform-first dependencies.
- Keep an eye on Kotlin/Native build times; modularize and cache to keep CI reasonable.
