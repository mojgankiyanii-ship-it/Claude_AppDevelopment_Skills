# iOS Interop (Kotlin/Native)

Where KMP projects live or die. Kotlin compiles to a native framework consumed by Swift — but the bridge has sharp edges. Get the exported API, async bridging, memory, and threading right.

## Contents
- How the bridge works
- SKIE (use it)
- Suspend & Flow to Swift
- Memory model & threading
- Exported API limitations
- Framework integration (SPM / CocoaPods)
- Common pitfalls

---

## How the bridge works

- The shared Kotlin module compiles to a native **framework** (an `XCFramework` for distribution) exposed to Swift through an **Objective-C** header. Swift sees an Obj-C-flavored view of your Kotlin API.
- That Obj-C intermediary is the source of most friction: Kotlin features that don't exist in Obj-C get flattened, renamed, or dropped. **Design the exported surface for Swift**, don't just expose internals.

## SKIE (use it)

- **SKIE** (by Touchlab) is a compiler plugin that massively improves the Swift-facing API. Strongly recommended on any serious iOS-targeting KMP project. It gives you:
  - **`suspend` functions as Swift `async`/`await`** (with cancellation), instead of completion handlers.
  - **`Flow` as Swift `AsyncSequence`**, so SwiftUI can `for await` updates naturally.
  - **Sealed classes as exhaustive Swift enums** (real `switch` exhaustiveness).
  - Better handling of default arguments and generics ergonomics.
- It's drop-in (a Gradle plugin) and removes most of the manual bridging boilerplate teams used to write.

## Suspend & Flow to Swift

- **With SKIE:** call `try await repository.fetch()` and `for await state in viewModel.state` directly in Swift. This is the clean path.
- **Without SKIE:** Kotlin `suspend` is exposed as a function taking a completion handler (`(Result, Error) -> Void`); `Flow` requires a manual collector wrapper that bridges to a closure/Combine publisher and returns a cancellable. You'll write a `FlowCollector`/wrapper helper. Prefer SKIE over maintaining this.
- Drive SwiftUI from observed state: map the shared `StateFlow` into an `@Observable`/`ObservableObject` view model on the Swift side that re-renders views on change.

## Memory model & threading

- The **modern Kotlin/Native memory manager is default** (since 1.7.20). **There is no more object freezing.** Coroutines run across threads; shared mutable state across threads works under normal concurrency discipline. Ignore any guidance about `freeze()`, `@SharedImmutable`, or `InvalidMutabilityException` — it's obsolete.
- Still mind threading: do UI updates on the main thread; run work on background dispatchers. Kotlin objects are reference-counted on iOS (ARC-integrated) — avoid retain cycles across the bridge (e.g. a Swift closure capturing a Kotlin object that holds the closure).
- Be deliberate about which dispatcher shared code uses so callbacks land where Swift expects.

## Exported API limitations (design around these)

- **Generics** are largely erased/limited across the Obj-C bridge — avoid exposing heavily generic public APIs to Swift.
- **Sealed classes** flatten to classes in plain interop (SKIE restores exhaustive enums).
- **Default arguments** are not exposed — Swift sees all parameters; provide overloads or builder-style APIs if needed.
- **Suspend/Flow** need SKIE or manual wrappers (above).
- **Inline/value classes, extension functions, overloads** map awkwardly — keep the public API plain and explicit.
- **Naming:** Kotlin/Swift naming differs; control it where it matters. Long Obj-C-style names can leak — design intentionally.
- Rule of thumb: the exported surface should be **plain classes/functions, concrete types, sealed enums (via SKIE), and async/AsyncSequence (via SKIE)**.

## Framework integration (SPM / CocoaPods)

- **Export the shared module as an `XCFramework`** for the iOS app to consume.
- Integration options:
  - **Direct Swift Package Manager (SPM)** export of the KMP framework — increasingly the preferred, lighter path; check your Kotlin version's support.
  - **CocoaPods** via the Kotlin CocoaPods Gradle plugin — long-standing, well-trodden, integrates with `pod install`.
  - A locally built XCFramework referenced by the Xcode project.
- For CI/distribution, publish the XCFramework (e.g. to a package repo) or build it as part of the iOS pipeline. Keep Kotlin/Xcode versions aligned.

## Common pitfalls

- **Skipping SKIE** and hand-writing Flow/suspend bridges → boilerplate and bugs. Use SKIE.
- **Exposing un-Swift-friendly APIs** (generics, defaults, sealed without SKIE) → painful Swift call sites. Design the surface.
- **Acting on outdated memory-model advice** (freezing) → wasted effort; it's gone.
- **Version mismatches** (Kotlin ↔ Compose MP ↔ coroutines ↔ Xcode) → cryptic native build failures. Align and pin.
- **Retain cycles across the bridge** → leaks; break cycles with weak references in Swift closures.
- **Doing heavy work on the main thread** in shared code called from Swift → UI jank. Dispatch correctly.
- **Forgetting to rebuild the framework** after Kotlin changes → Swift sees stale API. Wire the framework build into the iOS build.
