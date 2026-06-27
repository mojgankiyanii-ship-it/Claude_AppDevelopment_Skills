# Testing & Quality

Tests that give confidence to ship and refactor — focused on behavior, fast where they can be, realistic where they must be.

## Contents
- The test pyramid
- Unit tests
- Mocking
- Integration tests
- Testcontainers
- What to test
- Quality gates

---

## The test pyramid

- **Many unit tests** (fast, isolated, logic/domain) → **fewer integration tests** (the wire: API + DB + middleware) → **few end-to-end** (critical flows). Most value, least flake, comes from a solid base of unit tests plus targeted integration tests.
- Optimize for **confidence per second**: fast feedback on logic, realistic coverage on the seams where bugs hide (serialization, routing, auth, SQL).

## Unit tests

- **xUnit** is the common default (NUnit/MSTest fine). `[Fact]` for single cases, `[Theory]` + `[InlineData]`/`[MemberData]` for parameterized.
- Arrange-Act-Assert; one logical behavior per test; descriptive names (`Method_State_ExpectedResult`).
- Test **behavior and outcomes**, not implementation details — so refactors don't break tests.
- Fluent assertions (e.g. the FluentAssertions library) read clearly and fail with good messages.
- Keep units pure and fast: no DB, no network, no clock/Guid nondeterminism (inject `TimeProvider`/abstractions).

## Mocking

- Mock **dependencies you own and that represent boundaries** (an `IPaymentGateway`, a repository interface) — with Moq/NSubstitute.
- **Don't mock what you don't own** at the unit level (e.g. `DbContext`, `HttpClient`) — those lie; test them via integration tests instead.
- Don't over-mock: a test that mocks everything tests the mocks. Prefer real objects for value/domain types.
- Verify interactions only when the interaction *is* the behavior (a message was published); otherwise assert on outcomes.

## Integration tests

- **`WebApplicationFactory<TEntryPoint>`** spins up the real app in-memory and gives an `HttpClient` — test routing, model binding, validation, auth, serialization, and Problem Details against the actual pipeline.
- Override only what you must (swap external dependencies, configure test auth). Keep the rest real so you test the real wiring.
- Use a real database engine for data tests (see Testcontainers) rather than the in-memory provider — the in-memory provider doesn't behave like a relational DB (no real SQL, constraints, or transactions) and hides bugs.
- Reset state between tests (transaction rollback, respawn, or fresh container) for isolation.

## Testcontainers

- **Testcontainers for .NET** runs real Postgres/SQL Server/Redis/etc. in Docker for integration tests — production-like behavior without a shared test server.
- Spin up per test class/collection; apply migrations; tear down after. Catches real SQL, constraint, and migration issues the in-memory provider can't.
- Worth the slightly slower runs for the realism on data-access and messaging seams.

## What to test (and not)

- **Do:** domain rules and edge cases, validation, error/status-code mapping, serialization contracts, auth on endpoints, data-access queries (incl. N+1 and filtering), critical flows.
- **Skip:** trivial getters, the framework itself, and exhaustively re-testing what a type system already guarantees.
- Cover the unhappy paths explicitly — empty, invalid, unauthorized, conflict, downstream failure, cancellation.
- Treat a bug as missing coverage: write a failing test that reproduces it, then fix.

## Quality gates

- Nullable enabled; **warnings as errors**; analyzers (.NET analyzers / Roslyn) and a formatter (`dotnet format` / editorconfig) enforced in CI.
- CI runs build + tests on every PR; block merge on failure. Keep the suite fast enough to actually run.
- Coverage is a signal, not a target — chase meaningful coverage of logic and seams, not a percentage.
- Keep tests deterministic: control time, randomness, and ordering. A flaky test is a broken test.
