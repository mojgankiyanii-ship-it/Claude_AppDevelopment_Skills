# Environment Diagnostics & Incident Response

Calm, systematic diagnosis under pressure. Most "mysterious" failures are a recent change plus a signal you haven't looked at yet.

## Contents
- Incident response flow
- Systematic diagnosis
- The usual suspects
- Observability (the three pillars)
- Alerting that works
- Postmortems

---

## Incident response flow

1. **Stabilize before you diagnose.** Stop the bleeding — roll back the recent deploy, scale up, fail over, shed load, or flip the feature flag. Mitigation first; root cause second.
2. **Declare and coordinate.** For real incidents: assign an incident commander, a comms owner, and a scribe. One channel, one source of truth. Communicate to stakeholders early and regularly.
3. **Form a hypothesis from evidence**, not hunches: what changed recently (deploys, config, infra, traffic, dependencies) and what the signals say.
4. **Bisect to localize** — which service, which layer, which dependency, which change. Halve the search space each step.
5. **Smallest safe fix**, verified by signals. Then confirm recovery end-to-end (real user path, not just a green dashboard).
6. **Write it up** (blameless) and ship prevention.

## Systematic diagnosis

- **"What changed?" first.** ~80% of incidents follow a deploy, config change, cert/secret expiry, scaling event, or dependency change. Check the deploy/audit log before theorizing.
- **Follow the request path** end to end: client → DNS → LB/ingress → service → dependencies → DB → external APIs. Find where it actually breaks instead of guessing.
- **Read the signals in order:** is it up (health checks)? error rate and latency (metrics)? what do the logs/traces say at the failure point? Correlate by trace/correlation id.
- **Reproduce** in a lower environment if safe; isolate variables (one change at a time).
- **Compare working vs broken** (staging vs prod, before vs after, one region vs another) — the diff is the clue. "Works in staging, not prod" is almost always **config/secrets/permissions/data/scale drift** between environments.
- Don't tunnel: if a hypothesis isn't confirmed by evidence in a few minutes, widen back out.

## The usual suspects (env failures)

- **Config & secrets drift** between environments; expired/rotated secrets; missing env var.
- **Permissions/IAM** — the new role/policy that's too tight or too loose; cross-account/identity issues.
- **Networking** — security groups/NSGs, firewall rules, DNS resolution, service discovery, ingress/egress, MTU, port not open.
- **TLS/certs** — expired or untrusted certificates, SNI, chain issues.
- **Resource exhaustion** — CPU/memory limits (OOMKills), disk full, file descriptors, connection-pool/thread-pool exhaustion, ephemeral-port exhaustion.
- **Dependency/version** — an upstream change, a library/runtime bump, an image tag that moved, a region/AZ outage.
- **Data/scale** — a query that's fine on small data and dies at scale; N+1; missing index; hot partition; clock skew.
- **Capacity/quotas** — hitting a cloud service limit or quota.

## Observability (the three pillars)

- **Logs** — structured (JSON), with correlation/trace ids, levels, and context. Centralized and searchable. No secrets/PII. Logs answer "what exactly happened here."
- **Metrics** — numeric, cheap, aggregatable; alert on these. Two useful frames:
  - **RED** (request-driven services): Rate, Errors, Duration.
  - **USE** (resources): Utilization, Saturation, Errors.
- **Traces** — distributed tracing (OpenTelemetry) to follow a request across services and find the slow/failing hop. Propagate context everywhere.
- Tie them together with a shared **trace/correlation id**; expose it in errors so support can jump straight to the failure.
- Instrument for the **questions you'll ask at 3am**, not vanity dashboards.

## Alerting that works

- **Alert on symptoms users feel** (SLO burn, error rate, latency, availability), not raw causes (a single CPU spike). Cause-based alerts are noise.
- Every alert must be **actionable and urgent** — if no one needs to act now, it's a dashboard, not a page. Kill noisy alerts ruthlessly; alert fatigue is an outage waiting to happen.
- Page on user impact; ticket/notify on trends. Include context and a runbook link in the alert.
- Use **error budgets / SLO burn rates** to decide alert severity and urgency.

## Postmortems

- **Blameless** — focus on systems and contributing factors, not individuals. People act rationally given the info they had.
- Timeline, impact, root cause(s), what went well, what didn't, and **concrete action items with owners and dates**.
- Prefer systemic fixes (guardrails, automation, better signals) over "be more careful."
- Track action items to completion — a postmortem with no shipped prevention is theater.
