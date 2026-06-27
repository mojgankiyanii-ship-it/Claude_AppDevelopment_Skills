# Production Readiness

Observability, resilience, performance, security, and the shipping checklist for .NET services.

## Contents
- Observability
- Resilience
- Performance
- Security
- Configuration & secrets
- Deployment & health
- Ship checklist

---

## Observability

The three pillars, built in from the start:

- **Structured logging.** Use `ILogger<T>` with message templates and named properties — `_logger.LogInformation("Order {OrderId} placed for {Amount}", id, amount)` — never string-interpolate into the message (you lose structure and queryability). Serilog or the built-in logger to a structured sink. **Never log secrets, tokens, passwords, or PII.**
- **Metrics.** Emit app metrics (request rate, latency, error rate, queue depth, business counters) via `System.Diagnostics.Metrics` / OpenTelemetry. Track the things you'd page on.
- **Distributed tracing.** **OpenTelemetry** for traces across services; propagate context; use `Activity` for spans. Correlate logs/metrics/traces with a trace id.
- **Correlation ids** through middleware so a single request is traceable end-to-end. Surface the trace id in Problem Details so support can find the failure.

## Resilience

Outbound calls *will* fail; design for it:

- **Timeouts on everything.** No unbounded waits. Set `HttpClient` timeouts and DB command timeouts.
- **Retries with exponential backoff + jitter** for transient failures — but only for idempotent operations, with a cap. Don't retry a 400.
- **Circuit breakers** to stop hammering a failing dependency; fail fast and recover.
- **Bulkhead / concurrency limits** to isolate failures; **fallbacks** where a degraded response beats an error.
- Use the **`Microsoft.Extensions.Http.Resilience` / Polly** resilience pipelines wired into `HttpClientFactory` (`AddStandardResilienceHandler()`), not hand-rolled retry loops.
- Make retried operations **idempotent** (idempotency keys for writes/payments) so retries don't double-charge or duplicate.
- Rate limiting (built-in middleware) to protect the service from overload.

## Performance

- Async I/O end-to-end; never block the thread pool (no sync-over-async).
- **Fix the database first** — the right index, query, projection, and `AsNoTracking` beat micro-optimizing C#. Eliminate N+1.
- Cache expensive, staleness-tolerant reads (memory or Redis) with expiration.
- Minimize allocations on hot paths (spans, pooling, source-gen JSON); stream large payloads instead of buffering.
- Use `HttpClientFactory` (pooled handlers) — never `new HttpClient()` per call (socket exhaustion).
- Measure with real load and a profiler/BenchmarkDotNet before optimizing; set latency/throughput budgets.

## Security

- **Validate and sanitize all input** at the boundary; parameterized queries / EF (never string-concatenated SQL) — no injection.
- **AuthN/AuthZ** enforced server-side on every endpoint; least privilege; validate scopes/claims for the operation.
- **Secrets never in code or source control** — use a secret manager (Azure Key Vault, AWS Secrets Manager, user-secrets in dev). Rotate them.
- HTTPS/TLS everywhere; HSTS; secure headers; CORS locked to known origins (not `*` with credentials).
- Keep dependencies patched; scan for vulnerable packages (`dotnet list package --vulnerable`, dependabot). Follow OWASP API Top 10.
- Don't leak internals in errors; log security-relevant events; protect against over-posting, mass assignment, and excessive data exposure (DTOs help).
- Encrypt sensitive data at rest and in transit; hash passwords with a strong KDF if you store them.

## Configuration & secrets

- Typed options, validated on startup; environment-specific config via environment variables/secret store overriding files.
- Twelve-factor: config in the environment, not the build. Same artifact promoted across environments.
- Feature flags for risky rollouts; sane, safe defaults.

## Deployment & health

- **Health checks** (`AddHealthChecks`) with `/health/live` (process up) and `/health/ready` (dependencies reachable) for orchestrators/load balancers.
- Containerize with multi-stage builds; run as non-root; small base images; pin versions.
- Graceful shutdown: honor `IHostApplicationLifetime`/cancellation so in-flight requests drain before exit.
- Apply DB migrations as a controlled deploy step (job), not at app startup in prod. Use expand/contract for zero-downtime.
- CI/CD: build → test → scan → containerize → deploy; automated, repeatable, with rollback.

## Ship checklist

1. Async end-to-end; cancellation flowed; no sync-over-async.
2. Nullable on; warnings-as-errors; analyzers + format in CI.
3. Input validated at the edge; Problem Details for errors; no internals leaked.
4. AuthN/AuthZ enforced server-side; secrets in a vault; HTTPS/CORS correct.
5. EF queries reviewed (no N+1, projections, indexes, paging); migrations backward-compatible.
6. Timeouts, retries-with-backoff, circuit breakers on outbound calls; idempotency where retried.
7. Structured logging (no secrets/PII), metrics, tracing, correlation ids.
8. Health checks live/ready; graceful shutdown; config externalized.
9. Unit + integration (WebApplicationFactory + Testcontainers) tests green in CI.
10. Load-tested to budget; dependencies scanned for vulnerabilities.
