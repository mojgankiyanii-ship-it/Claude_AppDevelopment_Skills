---
name: dotnet-engineering
description: Engineer production-grade .NET and C# — modern language features, ASP.NET Core APIs, clean and vertical-slice architecture, EF Core data access, async and concurrency, dependency injection, testing, observability, resilience, security, and performance. Use whenever writing, designing, reviewing, or debugging .NET, C#, ASP.NET Core, Web APIs, microservices, or backend services, even when the user only says build an API, a service, or a feature.
---

# .NET Engineering

Build .NET backends that are correct, fast, observable, and maintainable. This skill encodes senior-level judgment for modern .NET (target a current LTS — .NET 8 or later) and ASP.NET Core, with a bias toward APIs, services, and microservices.

Apply it whenever the work touches C#, ASP.NET Core, Web APIs, EF Core, or backend services — even when the user just says "build an endpoint" or "fix this service."

## Operating principles

1. **Async all the way.** I/O is async end-to-end (`async`/`await`, `Task`). Never block on async (`.Result`, `.Wait()`, `.GetAwaiter().GetResult()`) — it deadlocks and starves the thread pool. Flow `CancellationToken` through everything.
2. **Lean on the framework.** Built-in DI, configuration, options, logging, `HttpClientFactory`, health checks, and `Microsoft.Extensions.*` are first-class. Don't hand-roll what the platform provides.
3. **Nullable reference types on.** `<Nullable>enable</Nullable>` everywhere. Treat warnings as errors. The compiler catches a whole class of null bugs for free.
4. **Model the domain honestly.** Make illegal states unrepresentable — records, enums, value objects, required members. Validate at the boundary; keep the core clean.
5. **Boundaries are explicit.** DTOs at the API edge (never expose EF entities directly), clear layers/slices, dependencies pointing inward. The web framework and the database are details, not the center.
6. **Observable by default.** Structured logging, metrics, and tracing are built in from day one, not bolted on after an incident.
7. **Configuration and secrets are external.** Nothing environment-specific or sensitive in code. Bind config to typed options; secrets come from a vault/secret store.

## Workflow

1. **Understand the context.** Target framework, app type (Web API / worker / gRPC / microservice), existing architecture, persistence (EF Core / Dapper), hosting (containers / cloud), and team conventions. Follow what exists.
2. **Design the contract first.** For APIs: resources, routes, verbs, request/response DTOs, status codes, error shape (Problem Details), versioning. For services: the public interface and its invariants.
3. **Model domain and data.** Entities, value objects, aggregates; the EF Core model and migrations; how transactions and consistency work.
4. **Implement the slice.** Endpoint → handler/service → data access, with DI, validation, async, and cancellation wired through. Keep handlers thin and focused.
5. **Handle the unhappy paths.** Validation failures, not-found, conflicts, downstream failures, timeouts, cancellation. Map them to correct status codes and Problem Details.
6. **Make it observable and resilient.** Structured logs with context, metrics, traces; timeouts, retries with backoff, and circuit breakers on outbound calls.
7. **Test and secure.** Unit tests for logic, integration tests for the wire; authn/authz, input validation, and secret handling verified.

## Defaults that prevent most problems

- **`async`/`await` with `CancellationToken`** on every I/O path; no sync-over-async. Use `await` not `.Result`. `ConfigureAwait(false)` in libraries (not needed in ASP.NET Core app code).
- **DI by constructor injection.** Register with correct lifetimes: **singleton** (stateless/shared), **scoped** (per request — e.g. `DbContext`), **transient** (lightweight, stateless). Never inject scoped into singleton (captive dependency).
- **Typed configuration** via the Options pattern (`IOptions<T>` / `IOptionsSnapshot<T>`); validate options on startup. Don't read raw `IConfiguration["..."]` scattered around.
- **Problem Details (RFC 7807/9457)** for error responses; consistent error contract; never leak stack traces to clients.
- **Validate input at the edge** (FluentValidation or `Validateable`/data annotations + a filter). Reject early with 400 + details.
- **DTOs in and out.** Map to/from domain/EF entities; don't bind or serialize EF entities directly (over-posting, cycles, leaking schema).
- **`HttpClientFactory`** for all outbound HTTP (never `new HttpClient()` in a loop — socket exhaustion); attach resilience handlers.
- **`DbContext` is scoped and not thread-safe** — one unit of work per request; don't share across threads or capture in singletons.
- **Log with structured templates** (`_logger.LogInformation("Order {OrderId} placed", id)`), never string interpolation into the message; never log secrets/PII.
- **Treat warnings as errors**; nullable enabled; analyzers on.

## API design quick rules

- Resource-oriented routes, correct verbs, correct status codes (200/201/204/400/401/403/404/409/422/500).
- Versioning from day one (URL or header). Pagination, filtering, and sorting as query params with sane caps.
- Idempotency for unsafe retried operations where it matters (payments!). Return `Location` on 201.
- Validate, authorize, then act. Authorize at the endpoint; don't rely on UI to hide things.

## Reference files

Load the relevant file when going deep:

- `references/language-csharp.md` — modern C#: async/concurrency, nullable, records, pattern matching, LINQ, error handling, performance primitives. Read for language-level work.
- `references/aspnetcore-apis.md` — ASP.NET Core: minimal APIs vs controllers, DI, options/config, middleware, validation, auth, REST/versioning/Problem Details. Read for API and web work.
- `references/architecture-data.md` — clean & vertical-slice architecture, DDD-lite, EF Core modeling, migrations, transactions, caching, the repository debate. Read for structure and persistence.
- `references/testing-quality.md` — xUnit, the test pyramid, mocking, integration tests with WebApplicationFactory, Testcontainers, code quality. Read when testing.
- `references/production.md` — observability (OpenTelemetry, structured logging), resilience (timeouts/retries/circuit breakers), performance, security, configuration & secrets, deployment & health checks. Read before shipping.
