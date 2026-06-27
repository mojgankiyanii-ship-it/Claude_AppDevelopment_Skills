---
name: devops-platform-engineering
description: Staff-level DevOps and platform engineering — environment diagnostics, incident response, CI/CD automation, infrastructure as code with Terraform, Kubernetes and containers, AWS and Azure, deployment strategies, migration auditing, observability, security, reliability, and cost. Use whenever building, debugging, reviewing, or operating pipelines, infrastructure, clusters, cloud resources, deployments, or production systems, even when the user only says deploy it, set up CI, or fix the environment.
---

# DevOps & Platform Engineering

Operate at staff level: think in systems, blast radius, and tradeoffs — not just commands. The job is reliable, secure, repeatable delivery of software and infrastructure, and fast, calm diagnosis when something breaks. Reach for the simplest thing that meets the reliability, security, and cost bar; complexity is a liability you must justify.

Apply this whenever the work touches CI/CD, infrastructure, containers, clusters, cloud resources, deployments, environments, or production operations — even when the user just says "deploy it" or "fix the environment."

## The staff-level mindset

1. **Everything as code, reviewed and versioned.** Infra, pipelines, config, and policy live in git, are peer-reviewed, and are reproducible. No click-ops in production; no snowflake servers.
2. **Reduce blast radius.** Small, reversible changes. Know what breaks if this fails, and how to undo it *before* you ship. Roll-forward and rollback are both first-class.
3. **Automate the path, pave the road.** Make the right way the easy way. Manual runbooks become pipelines; tribal knowledge becomes golden paths other teams can reuse.
4. **Observability before you need it.** You can't operate what you can't see. Logs, metrics, traces, and good alerts are part of "done," not an afterthought.
5. **Secure and least-privilege by default.** Identity over network position; short-lived credentials; secrets in a manager; supply chain and IaC scanned. Security is built in, not bolted on.
6. **Design for failure.** Things will fail — dependencies, nodes, zones, deploys. Build timeouts, retries, health checks, redundancy, and graceful degradation. Test the failure, including restore-from-backup.
7. **Cost and toil are engineering concerns.** Right-size, set budgets/alerts, and automate toil away. Cheap-and-reliable beats clever.

## Operating workflow

For a **build/change** (pipeline, infra, deployment):
1. **Clarify intent and constraints** — what, which environments, SLAs, compliance, existing stack and conventions. Follow what's there.
2. **Design for safety** — change strategy (rolling/blue-green/canary), rollback plan, blast radius, dependencies, and what to observe.
3. **Codify it** — IaC for infra, pipeline-as-code for delivery, policy-as-code for guardrails. Modular, parameterized, environment-promotable.
4. **Add the safety net** — health checks, automated tests/validation in the pipeline, observability, alerts, and a documented rollback.
5. **Ship progressively** — to lower environments first, then prod via a controlled, observable rollout. Verify with real signals, not hope.

For a **diagnosis/incident**, see `references/diagnostics.md`: stabilize first, form a hypothesis from evidence (recent changes + signals), bisect, fix the smallest thing, then write it up and prevent recurrence.

## Migration auditing (called out, because it bites hard)

Before approving any migration — database schema, infra/Terraform state, cloud platform, version/runtime upgrade — audit it against:

- **Backward compatibility / zero-downtime:** does the app keep working *during* the rollout? Use **expand → migrate/backfill → contract** so old and new run together. No destructive change in a single step on a live system.
- **Rollback:** is there a tested way back? Irreversible migrations (dropping columns, deleting data) need extra gates, backups, and a decision record.
- **Data integrity & blast radius:** what data is touched, how much, can it be corrupted, what's the worst case, who's affected.
- **Ordering & coupling:** correct order of app deploy vs. migration; feature flags to decouple deploy from release.
- **State moves (IaC):** Terraform `moved` blocks / `state mv` over destroy-recreate; review the plan for unexpected replacements (the #1 IaC incident).
- **Verification:** how you'll confirm success, and the abort criteria. Run it in a prod-like environment first; take a backup; have the restore path tested.

Treat "the migration ran" as the start, not the finish — verify integrity and have the rollback ready.

## Defaults that prevent most incidents

- **IaC for all infra** (Terraform default); remote, locked, encrypted state; modules; `plan` reviewed before `apply`; policy-as-code guardrails. Never destroy-recreate stateful resources without intent.
- **Pipeline-as-code** with stages: lint → test → build → scan → deploy; same artifact promoted across environments; deploys are automated and idempotent.
- **Immutable, versioned artifacts** (containers, pinned by digest); no mutating servers in place.
- **Progressive delivery** (rolling/canary/blue-green) with health checks and automated rollback on failure.
- **Secrets in a manager** (Vault / cloud secret store), short-lived, never in repos or images; OIDC for CI to cloud instead of long-lived keys.
- **Health checks + graceful shutdown** on every service; readiness gates traffic, liveness restarts.
- **Observability**: structured logs, RED/USE metrics, traces, correlation ids, and alerts tied to user-facing SLOs (not noisy CPU graphs).
- **Backups that are tested by restore**; DR plan with real RTO/RPO.
- **Least privilege IAM**; network segmentation; encryption in transit and at rest.

## Reference files

Load the relevant file when going deep:

- `references/diagnostics.md` — environment diagnostics, systematic debugging, incident response, observability (logs/metrics/traces/alerts). Read when something is broken or you're building observability.
- `references/cicd.md` — CI/CD pipelines, GitOps, deployment strategies, release & migration auditing, artifacts, testing in the pipeline. Read for delivery automation.
- `references/iac.md` — Infrastructure as Code: Terraform (state, modules, workspaces, drift, policy), Pulumi/CDK, Ansible, GitOps for infra. Read for provisioning.
- `references/kubernetes.md` — containers & Kubernetes: workloads, networking, scaling, storage, security, Helm, troubleshooting. Read for cluster work.
- `references/cloud.md` — AWS & Azure: core compute/network/storage/identity, Well-Architected, multi-account/subscription, cost. Read for cloud architecture.
- `references/security-reliability.md` — DevSecOps, secrets, supply chain, SRE (SLO/SLI, error budgets, on-call), scaling, resilience, cost optimization. Read for hardening and operating at scale.
