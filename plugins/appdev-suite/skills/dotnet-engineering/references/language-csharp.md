# Modern C# & the Language

Write idiomatic, safe, modern C#. The compiler and type system can prevent whole categories of bugs if you let them.

## Contents
- Async & concurrency
- Nullable reference types
- Types & domain modeling
- Pattern matching & expressions
- LINQ & collections
- Error handling
- Performance primitives

---

## Async & concurrency

- **Async all the way down.** If a method does I/O, it's `async Task`/`Task<T>` and awaits. Don't mix sync and async.
- **Never block on async:** no `.Result`, `.Wait()`, `.GetAwaiter().GetResult()` — deadlocks (in some contexts) and thread-pool starvation. If you have async, await it.
- **Flow `CancellationToken`** through every async call; honor it (`token.ThrowIfCancellationRequested()`, pass to EF/HTTP/IO). ASP.NET Core gives you `HttpContext.RequestAborted`.
- **`ConfigureAwait(false)`** in library code (no sync context needed); unnecessary in ASP.NET Core app code (no sync context).
- **Don't `async void`** except for event handlers — exceptions are unobservable. Use `async Task`.
- **`ValueTask`** only for hot paths that often complete synchronously; default to `Task`.
- **Parallel I/O:** `await Task.WhenAll(...)` for independent calls. For CPU-bound parallelism, `Parallel`/PLINQ — but most server work is I/O-bound, so prefer async concurrency.
- **Thread safety:** prefer immutability and per-request scope over locks. `DbContext`, most EF objects, and many clients are **not** thread-safe.
- **`IAsyncEnumerable<T>`** + `await foreach` for streaming/async iteration.

## Nullable reference types

- Enable globally: `<Nullable>enable</Nullable>`. Treat nullable warnings as errors.
- Let the annotations document intent: `string` is non-null, `string?` may be null. Don't suppress with `!` unless you can prove non-null.
- Guard at boundaries; inside well-typed code you shouldn't need null checks everywhere.
- Use `required` members and constructor parameters so objects can't be half-initialized.

## Types & domain modeling

- **`record` / `record struct`** for immutable data and value semantics (DTOs, value objects, events). `with` for non-destructive copies.
- **`readonly struct`** for small immutable values you don't want heap-allocated.
- **Value objects** (e.g. `Email`, `Money`) wrap primitives with validation — kill "primitive obsession" and invalid states.
- **Enums** for closed sets; consider the smart-enum pattern when behavior attaches to values.
- **`init`-only and `required`** to build immutable-after-construction objects.
- Make illegal states unrepresentable: model `Result`/`OneOf`-style outcomes or discriminated-union-like patterns instead of returning null + out-params + bool flags.

## Pattern matching & expressions

- Switch expressions for mapping and dispatch — exhaustive, terminal, readable.
- Property, positional, relational, and list patterns to destructure and branch clearly.
- `is`/`is not` null and type patterns over verbose `if` chains.
- Prefer expression-bodied members where it improves clarity.

## LINQ & collections

- LINQ for clarity on in-memory collections. Be aware of **deferred execution** — materialize with `ToList()`/`ToArray()` when you need to iterate multiple times or capture a snapshot.
- **EF Core LINQ runs in the database** — keep queries translatable; avoid client-side evaluation of heavy filters. Watch for accidental N+1 (see data reference).
- Pick the right collection: `List<T>` general, `Dictionary`/`HashSet` for lookups, `IReadOnlyList`/`IReadOnlyCollection` on public surfaces, `ImmutableArray`/`FrozenDictionary` for read-heavy shared data.
- Return `IEnumerable<T>`/`IReadOnlyList<T>` from APIs rather than concrete mutable types; don't return `null` for a collection — return empty.

## Error handling

- **Exceptions for exceptional/unrecoverable cases**; don't use them for normal control flow (they're costly and noisy).
- For expected failures (validation, not-found, business rule violations), prefer a **Result type** or explicit outcomes over throwing — clearer and faster on hot paths.
- Catch specific exceptions; never swallow (`catch {}`). Add context, then rethrow with `throw;` (not `throw ex;` — preserves stack).
- Custom exceptions for domain errors; map them centrally to HTTP status + Problem Details (see API reference).
- Validate arguments at public boundaries (`ArgumentNullException.ThrowIfNull(x)`, guard clauses).
- Don't log-and-rethrow at every layer (duplicate logs); handle/observe once, usually at the boundary.

## Performance primitives (when it matters)

- **`Span<T>`/`ReadOnlySpan<T>`/`Memory<T>`** for slicing without allocation (parsing, buffers).
- **`StringBuilder`** for heavy string building; string interpolation handlers are efficient for logging.
- **`ArrayPool<T>`** / pooling for high-throughput buffer reuse.
- **`System.Text.Json`** (source-generated context for AOT/perf) over Newtonsoft unless you need its features.
- Measure before optimizing (BenchmarkDotNet). Most server cost is I/O and allocations, not raw CPU — fix the query and the allocations first.
