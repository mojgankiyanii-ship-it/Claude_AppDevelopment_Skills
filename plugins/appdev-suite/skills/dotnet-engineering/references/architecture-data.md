# Architecture & Data (EF Core)

Structure for change, and treat the database as a detail you control deliberately.

## Contents
- Architecture styles
- Domain modeling (DDD-lite)
- EF Core fundamentals
- Querying well (and N+1)
- Transactions & consistency
- Migrations
- Caching
- The repository debate

---

## Architecture styles

- **Vertical slices** — organize by feature (each slice owns its endpoint, handler, validation, data access). Scales well for APIs/microservices, minimizes cross-feature coupling, easy to delete a feature. Often the best default for service-style apps.
- **Clean / onion / hexagonal** — concentric layers with dependencies pointing inward: domain core (entities, value objects, rules) has no framework dependencies; application layer orchestrates; infrastructure (EF, HTTP, messaging) implements interfaces defined inward; web is the outer detail. Good for complex domains and long-lived systems.
- **Pragmatism over purity.** Don't impose five layers on a CRUD service. Match ceremony to complexity. The non-negotiable is: **domain logic doesn't depend on EF or ASP.NET**; the database and the web framework are details.
- **Microservices:** own your data per service, communicate via well-defined contracts (HTTP/gRPC/events), design for partial failure, and don't share a database across services. Keep services independently deployable.

## Domain modeling (DDD-lite)

- Rich entities and value objects that protect their invariants — not anemic bags of public setters mutated from services.
- **Aggregates** define consistency boundaries; reference other aggregates by id, not by navigation, across boundaries.
- Keep business rules in the domain; keep orchestration (transactions, calling repos, publishing events) in the application layer.
- Domain events for side effects that should follow a state change.
- Don't over-engineer: a simple service may not need aggregates and events. Add structure when the domain earns it.

## EF Core fundamentals

- **`DbContext` is scoped, lightweight, and not thread-safe** — one per request/unit of work. Don't share across threads, don't capture in singletons, dispose per scope (DI handles this).
- Configure the model explicitly with `IEntityTypeConfiguration<T>` (fluent API) rather than scattering data annotations; keep mapping out of domain types where you can.
- Map value objects with owned types/converters. Use `required`/backing fields to keep entities encapsulated.
- Prefer async EF methods (`ToListAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`) with a `CancellationToken`.

## Querying well (and N+1)

- **N+1 is the classic EF performance bug:** lazy/implicit loading inside a loop fires a query per item. Fix with `Include`/`ThenInclude` (eager) or projection.
- **Project to DTOs with `Select`** when you don't need the full entity — fetch only the columns you use; faster and avoids loading graphs.
- **`AsNoTracking()`** for read-only queries — skips change-tracking overhead.
- Keep queries **translatable to SQL**; avoid pulling a table into memory then filtering (client evaluation). Watch for functions EF can't translate.
- Page large result sets; never `ToList()` an unbounded table. Add indexes for your real query patterns.
- Inspect generated SQL when performance matters; EF is convenient, not magic.

## Transactions & consistency

- `SaveChangesAsync` wraps a single call in a transaction. For multi-step units of work, use an explicit transaction or the unit-of-work pattern around one `DbContext`.
- Handle concurrency: optimistic concurrency via a `rowversion`/concurrency token; catch `DbUpdateConcurrencyException` and resolve.
- Across services/resources, you can't rely on distributed transactions — use the **outbox pattern** and idempotent consumers for reliable messaging, and design for eventual consistency.

## Migrations

- Code-first migrations checked into source control; review the generated SQL.
- Make migrations **backward-compatible** for zero-downtime deploys (expand/contract: add nullable column → backfill → enforce → remove old in a later release). Never destructive-in-place on a live system.
- Apply migrations as a controlled deploy step (a migration job/CLI), not silently at app startup in production.
- Seed reference data deliberately and idempotently.

## Caching

- Cache reads that are expensive and tolerant of staleness. `IMemoryCache` for single-instance; **distributed cache** (Redis) for multi-instance/microservices.
- Always set expiration; have an invalidation strategy; guard against cache stampede.
- Don't cache per-user/sensitive data without scoping the key; don't let the cache become a second source of truth.

## The repository debate

- **`DbContext` is already a unit of work and `DbSet<T>` is already a repository.** Wrapping EF in generic repositories often adds indirection without value and leaks `IQueryable` anyway.
- Worth a repository/abstraction when: you must keep the domain free of EF, you need to swap providers, or you want a narrow, intention-revealing data API per aggregate. Then make it **specific** (`IOrderRepository` with real methods), not a generic `IRepository<T>`.
- Default to using EF directly in a thin data layer / slice; abstract only when a concrete need justifies it.
