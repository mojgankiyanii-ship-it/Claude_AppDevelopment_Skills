# CI/CD, Deployment & Migration Auditing

Reliable, repeatable delivery: build once, promote everywhere, deploy safely, and audit risky migrations before they bite.

## Contents
- Pipeline design
- CI stages
- Artifacts & promotion
- Deployment strategies
- GitOps
- Database & migration auditing
- Rollback & safety

---

## Pipeline design

- **Pipeline-as-code** in the repo (GitHub Actions, GitLab CI, Azure Pipelines, etc.), reviewed like any code.
- **Fast feedback:** order stages cheap-and-quick first (lint, unit) so failures surface in minutes; gate expensive stages behind them. Cache dependencies; parallelize.
- **Idempotent and repeatable:** re-running a deploy produces the same result. No manual steps in the critical path.
- **One pipeline, many environments:** the same pipeline promotes the same artifact through dev → staging → prod with environment-specific config injected, not rebuilt.
- **Least privilege for CI:** use **OIDC federation** to assume short-lived cloud roles instead of storing long-lived cloud keys. Scope permissions per pipeline/environment. Protect prod with required reviewers/environments.

## CI stages (typical)

`lint/format → unit tests → build → security & quality scans → package artifact → integration tests → deploy (gated) → smoke/verify`

- **Scans:** SAST, dependency/SCA (vulnerable packages), secret scanning, IaC scanning (tfsec/checkov), container image scanning. Fail the build on real findings; don't let security be optional.
- **Quality gates:** tests + coverage thresholds + lint as merge-blocking checks.
- **Supply chain:** pin dependencies, generate an SBOM, sign artifacts/images (e.g. cosign), and verify signatures at deploy.

## Artifacts & promotion

- **Build once, promote the same artifact.** Never rebuild per environment — that's how "works in staging, breaks in prod" happens.
- **Immutable, versioned, digest-pinned** container images (or packages). Tag with a traceable version + git SHA. Don't deploy `latest`.
- Store in a registry/artifact repo with retention and provenance. Config and secrets are injected at deploy, not baked into the artifact.

## Deployment strategies

- **Rolling** — replace instances gradually behind health checks. Simple default; needs backward-compatible changes.
- **Blue-green** — stand up the new version alongside, switch traffic atomically, keep old for instant rollback. Great for fast, clean cutover/rollback; costs double during the window.
- **Canary** — route a small % to the new version, watch SLOs, ramp up or auto-roll-back. Best blast-radius control; needs good metrics and automation.
- **Feature flags** — decouple **deploy** (code in prod, dark) from **release** (turning it on). Ship continuously, release gradually, kill instantly. The most powerful risk tool you have.
- Every strategy needs **readiness/health gating** and **automated rollback on failure signals**.

## GitOps

- Git is the source of truth for desired state; an agent (Argo CD, Flux) continuously reconciles the cluster/infra to match.
- Benefits: auditable history, easy rollback (revert the commit), drift detection and self-healing, no out-of-band kubectl/click-ops.
- Separate app config repos from infra; use PRs + policy checks as the change gate; protect the prod path.

## Database & migration auditing

The highest-risk part of most deploys. Before approving any migration (schema, data, infra state, platform, runtime upgrade), audit:

- **Zero-downtime via expand/contract:**
  1. **Expand** — add new (nullable column, new table, new index built concurrently). Backward-compatible.
  2. **Migrate/backfill** — dual-write or backfill data; deploy app that works with old *and* new.
  3. **Contract** — only after the old code is fully gone, remove the old column/table in a later release.
  Never rename/drop/retype in a single step on a live system.
- **Deploy ordering:** usually migrate (additively) → deploy app → backfill → later cleanup. Decouple with feature flags. Make app tolerant of both schema versions during rollout.
- **Rollback:** is the migration reversible? Irreversible/destructive ones need a backup, extra approval, and a recorded decision. Forward-fixes are often safer than rolling a schema back.
- **Blast radius & integrity:** how many rows, locks/long transactions (can lock a hot table — use batched/online migration tools), worst-case data corruption, who's affected.
- **IaC state migrations:** prefer `moved` blocks / `state mv` / `import` over destroy-recreate. **Read the plan** for unexpected `-/+ replace` on stateful resources — the classic "Terraform deleted prod" incident.
- **Verification & abort:** define success checks, monitoring during the run, and abort criteria. Always rehearse in a prod-like environment with a tested restore path first.

## Rollback & safety

- **Define rollback before deploying.** Know the exact command/commit and that it's tested.
- Automated rollback triggered by health checks / SLO burn during progressive rollout.
- Keep N-1 deployable; keep migrations backward-compatible so the previous app version still runs.
- Smoke-test the real user path post-deploy; a green pipeline is not proof of working software.
- Deploy during low-risk windows for high-impact changes; avoid Friday-evening destructive migrations.
