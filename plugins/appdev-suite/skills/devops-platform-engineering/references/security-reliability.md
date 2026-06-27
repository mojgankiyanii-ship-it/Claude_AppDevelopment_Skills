# Security (DevSecOps) & Reliability (SRE)

Hardening and operating at scale: build security into the pipeline, and run systems to explicit reliability targets.

## Contents
- DevSecOps: shift left
- Secrets management
- Supply chain
- SRE: SLO/SLI/error budgets
- On-call & toil
- Resilience & DR
- Scaling
- Cost as reliability

---

## DevSecOps: shift left

Security is a pipeline stage, not a gate at the end.

- **In CI:** SAST (code), SCA (dependencies/CVEs), secret scanning, IaC scanning (tfsec/checkov), container image scanning, and license checks. Fail builds on real findings.
- **Least privilege everywhere:** IAM, K8s RBAC, CI permissions, DB grants. Default-deny; grant the minimum.
- **Network segmentation** and default-deny policies; private by default; encrypt in transit and at rest.
- **Patch management:** keep base images, runtimes, and dependencies current; automate dependency PRs; track CVEs.
- **Detection:** centralized audit logs (CloudTrail/Activity Log), alert on sensitive/anomalous actions, and a tested incident response plan.
- Follow OWASP (Top 10 / API Top 10) and CIS benchmarks for baselines.

## Secrets management

- **Never in code, repos, images, or plain env files in git.** Secret scanning blocks accidents.
- Use a **secret manager** (Vault, AWS Secrets Manager, Azure Key Vault) with access policies and audit logs.
- **Short-lived / dynamic credentials** over static ones; rotate regularly and automatically; rotate immediately on exposure.
- **CI → cloud via OIDC** (assume short-lived roles) instead of stored cloud keys.
- In Kubernetes, prefer External Secrets Operator / CSI driver over raw Secrets; enable encryption at rest.

## Supply chain

- Pin and verify dependencies; generate an **SBOM**; **sign** artifacts/images (cosign) and verify at deploy.
- Trusted base images and registries only; scan continuously (new CVEs land after build).
- Protect the pipeline itself — it has powerful credentials; treat CI as production. Review third-party actions/plugins (they run with your permissions).

## SRE: SLO / SLI / error budgets

- **SLI** — a measured signal of user happiness (availability, latency, error rate, correctness).
- **SLO** — the target for an SLI (e.g. 99.9% of requests < 300ms over 30 days). Set from user need, not vanity.
- **Error budget** — the allowed unreliability (100% − SLO). It's a *budget to spend*: ship fast while in budget; slow down and harden when you're burning it. This turns "how reliable?" into an engineering decision, not an argument.
- **Alert on SLO burn rate**, not raw resource metrics. Page on user-facing symptoms.
- 100% is the wrong target — it's infinitely expensive and users can't tell. Pick the right nines for the workload.

## On-call & toil

- **Toil** = manual, repetitive, automatable operational work with no lasting value. Measure it; cap it; automate it down. Rising toil is a scaling failure.
- Humane on-call: actionable alerts only, sane rotations, runbooks, and follow-the-sun where possible. Every page should be necessary and fixable.
- Feed incidents back into prevention (postmortem action items → automation/guardrails).

## Resilience & DR

- **Design for failure:** timeouts, retries with backoff+jitter, circuit breakers, bulkheads, graceful degradation, idempotency. Assume dependencies, nodes, and zones fail.
- **Redundancy** across AZs/regions appropriate to the SLO; no single points of failure on critical paths.
- **Backups tested by restore** — an untested backup is a hope, not a plan. Know your **RTO** (time to recover) and **RPO** (data-loss window) and verify you meet them.
- **DR plan** rehearsed (game days / chaos experiments). Document failover and run it before you need it.
- Health checks, autoscaling, and load shedding to absorb spikes and partial failure.

## Scaling

- **Horizontal over vertical** for stateless services; statelessness enables it. Externalize state.
- **Cache** expensive/hot reads (with expiration + invalidation); use queues to absorb spikes and decouple producers/consumers.
- Find and remove **bottlenecks** with data (often the database — connections, locks, missing indexes, hot keys) before adding machines.
- Autoscale on the metric that reflects real load; set min/max and stabilization; load-test to known limits and budgets.
- Manage **connection pools and quotas** — exhaustion is a top scale-failure mode.

## Cost as reliability

- Waste competes with reliability for budget — both are engineering outcomes. Right-size, schedule nonprod off-hours, kill orphans, and use commitments/spot appropriately.
- Tag for attribution; set budgets and anomaly alerts; review monthly. Watch storage tiering and data egress.
- The cheapest reliable design usually has the fewest moving parts — prefer boring, managed, and simple unless complexity is earned.
