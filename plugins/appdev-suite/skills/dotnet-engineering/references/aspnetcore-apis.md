# ASP.NET Core & API Design

Building HTTP APIs and services. Lean on the framework; keep the edge thin and the contract clean.

## Contents
- Minimal APIs vs controllers
- Dependency injection
- Configuration & options
- Middleware & the pipeline
- Validation
- Error handling (Problem Details)
- Authentication & authorization
- REST, versioning, contracts

---

## Minimal APIs vs controllers

- **Minimal APIs** — lean, fast, great for microservices and focused APIs. Group with route groups; keep handlers thin and delegate to services/handlers. Pair with a vertical-slice structure.
- **Controllers** — richer conventions, model binding, filters, and tooling; good for large APIs and teams that want the structure.
- Either way: handlers/actions stay **thin** — bind, validate, call a service/handler, map the result to a response. No business logic or data access in the endpoint.
- Return typed results (`Results<Ok<T>, NotFound, ...>` / `TypedResults`) so the contract and OpenAPI are precise.

## Dependency injection

- **Constructor injection** is the default. The built-in container is enough for most apps.
- **Lifetimes:**
  - **Singleton** — stateless, thread-safe, shared (config, `HttpClientFactory`-backed clients, caches).
  - **Scoped** — per request (`DbContext`, unit-of-work, request-specific services).
  - **Transient** — lightweight, stateless, new each time.
- **Captive dependency danger:** never inject Scoped/Transient into a Singleton. Don't resolve scoped services from the root provider. Use `IServiceScopeFactory` when a singleton genuinely needs a scope (e.g. background work).
- Register against interfaces for testability; keep registration organized (extension methods like `AddXModule()`).
- Prefer composition over service-locator (`IServiceProvider.GetService` inside code) — inject what you need.

## Configuration & options

- **Options pattern:** bind config sections to typed classes; inject `IOptions<T>` (singleton snapshot), `IOptionsSnapshot<T>` (per-request, reloadable), or `IOptionsMonitor<T>` (change notifications).
- **Validate options** at startup (`ValidateDataAnnotations().ValidateOnStart()`) so misconfig fails fast, loudly, at boot — not at 3am in prod.
- Layer sources: appsettings.json → environment-specific → environment variables → secret store. Environment and secrets override files.
- Don't scatter `IConfiguration["Key"]` reads through the codebase; centralize into typed options.

## Middleware & the pipeline

- Order matters: exception handling first, then HTTPS/HSTS, routing, CORS, authn, authz, then endpoints. Get the order right or auth/CORS silently misbehave.
- Use built-in middleware (exception handler, status code pages, response compression, rate limiting) before writing custom.
- Cross-cutting concerns (logging context, correlation IDs, tenant resolution) belong in middleware, not in every handler.

## Validation

- Validate request DTOs at the boundary. **FluentValidation** for non-trivial rules; data annotations for simple cases.
- Centralize: a filter/endpoint filter that runs validation and returns **400 + Problem Details** with field errors — don't hand-write checks in each handler.
- Validate semantics (business rules) in the domain/service layer separately from shape validation at the edge.
- Guard against over-posting by binding to purpose-built request DTOs, not entities.

## Error handling (Problem Details)

- Use **`ProblemDetails` (RFC 7807/9457)** as the single error contract. Configure `AddProblemDetails()` and a global exception handler (`IExceptionHandler` / exception-handling middleware).
- Map exceptions/outcomes to status codes centrally: validation → 400/422, unauthorized → 401, forbidden → 403, not-found → 404, conflict → 409, unhandled → 500.
- **Never leak stack traces or internals** to clients in production. Log the detail server-side with a correlation/trace id; return a clean Problem Details with that id.

## Authentication & authorization

- **AuthN:** JWT bearer / OpenID Connect for APIs; validate issuer, audience, lifetime, signing key. Use a trusted IdP (Entra ID, Auth0, Keycloak, Cognito) — don't roll your own token issuance unless you must.
- **AuthZ:** policy-based authorization (`[Authorize(Policy=...)]` / `RequireAuthorization`) over scattered role checks. Resource-based authorization for "can this user act on this object."
- **Authorize at the endpoint**, server-side, every time. Never rely on the client hiding actions.
- HTTPS everywhere; HSTS; secure cookies (`HttpOnly`, `Secure`, `SameSite`) when using cookies; CSRF protection for cookie-auth browser flows.
- Least privilege; validate scopes/claims for the specific operation.

## REST, versioning, contracts

- Resource-oriented URLs, correct verbs (GET safe/idempotent, PUT idempotent, POST create, PATCH partial, DELETE), correct status codes.
- **Version from day one** (`/v1/...` or header). Don't break existing clients; add, don't mutate.
- **Pagination/filtering/sorting** via query params with enforced caps and stable ordering. Return total/next where useful.
- **Idempotency** for retried unsafe operations (especially payments) via idempotency keys.
- Publish **OpenAPI**; keep it accurate (typed results help). Return `Location` on 201 Created.
- Be conservative in what you return and liberal in what you accept — but validate strictly.
