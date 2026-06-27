# Containers & Kubernetes

Run containers reliably at scale. Most cluster pain is resource limits, health probes, networking, and YAML drift.

## Contents
- Containers done right
- Workloads
- Health & resources
- Networking
- Scaling
- Storage & state
- Security
- Helm & packaging
- Troubleshooting

---

## Containers done right

- **Small, minimal base images** (distroless/alpine/slim); multi-stage builds; only runtime deps in the final image.
- **Run as non-root**, read-only root filesystem where possible, drop capabilities. One process per container.
- **Pin by digest**, not `latest`. Scan images for vulnerabilities in CI. Don't bake secrets into layers.
- **Stateless containers**; externalize state (DB, object store, cache). Config via env/mounted config; secrets via secret store.
- Handle **SIGTERM** for graceful shutdown; respect the termination grace period.

## Workloads

- **Deployment** for stateless apps (rolling updates, replicas). **StatefulSet** for stable identity/storage (databases, brokers). **DaemonSet** for per-node agents. **Job/CronJob** for batch/scheduled.
- Set **rolling update** params (maxUnavailable/maxSurge) and a **PodDisruptionBudget** so voluntary disruptions don't take you below quorum.
- Spread replicas across nodes/zones (topology spread, anti-affinity) for real availability.

## Health & resources (the two biggest footguns)

- **Probes:**
  - **Readiness** — gates traffic; fail it and the pod is removed from the Service until ready (use for warmup/dependencies).
  - **Liveness** — restarts a wedged pod; keep it cheap and tolerant or you'll create restart loops.
  - **Startup** — protects slow-starting apps from premature liveness kills.
  Wrong probes cause both "no traffic" and "constant restarts" incidents.
- **Requests and limits:**
  - **Requests** drive scheduling and guaranteed resources; set them from real usage.
  - **Memory limit exceeded = OOMKill**; CPU limit = throttling. Set memory limits carefully; many teams avoid CPU limits to prevent throttling and set requests well.
  - Unset requests → bad scheduling and noisy-neighbor problems. This is the #1 cause of mysterious cluster behavior.

## Networking

- **Service** (ClusterIP) for stable in-cluster addressing; **Ingress / Gateway API** for HTTP(S) routing in; **LoadBalancer** for L4.
- **NetworkPolicies** to restrict pod-to-pod traffic (default-deny, then allow) — segmentation matters.
- DNS via CoreDNS; service discovery by name. Many "can't connect" issues are NetworkPolicy, DNS, or a wrong port/selector.
- A **service mesh** (Istio/Linkerd) adds mTLS, traffic shaping, and telemetry — adopt only when the complexity is justified.

## Scaling

- **HPA** scales replicas on CPU/memory/custom metrics. **VPA** right-sizes requests. **Cluster Autoscaler / Karpenter** adds/removes nodes.
- Scale on the metric that reflects load (often a custom/queue metric, not just CPU). Set sane min/max and stabilization to avoid flapping.
- Combine pod + node autoscaling; ensure requests are accurate or autoscaling misfires.

## Storage & state

- **PersistentVolumeClaims** with appropriate StorageClasses for stateful workloads; understand access modes and reclaim policy.
- Prefer **managed databases** over self-hosting stateful systems in-cluster unless you have a strong reason and the expertise.
- Back up persistent data and test restores; StatefulSet data is still your responsibility.

## Security

- **RBAC** least-privilege for users and service accounts; no cluster-admin by default.
- **Pod Security** standards (restricted), non-root, drop capabilities, seccomp, read-only FS.
- **Secrets:** Kubernetes Secrets are base64, not encrypted by default — enable encryption at rest and prefer an external secret store (External Secrets Operator + Vault/cloud).
- Scan images and manifests; sign images; restrict registries. NetworkPolicies for segmentation.
- Keep the cluster and nodes patched; limit the API server exposure.

## Helm & packaging

- **Helm** for templated, versioned, parameterized releases; values per environment; `helm diff` before upgrade; rollback support.
- **Kustomize** for overlay-based, template-free environment variants. Use either consistently; avoid mixing chaotically.
- Manage releases via **GitOps** (Argo CD/Flux) rather than imperative `kubectl apply` to prod.

## Troubleshooting (fast paths)

- `kubectl get pods` → status tells a lot: **CrashLoopBackOff** (app crashing — check logs), **ImagePullBackOff** (bad image/registry/secret), **Pending** (no schedulable node / unsatisfiable requests), **OOMKilled** (raise memory or fix leak), **Evicted** (node pressure).
- `kubectl describe pod` for events (scheduling, probe failures, mounts); `kubectl logs` (`--previous` for crashed); `kubectl exec` to poke inside.
- Check **events** (`kubectl get events --sort-by=...`), resource pressure (`kubectl top`), and NetworkPolicy/DNS for connectivity.
- "Works locally, not in cluster" → usually requests/limits, probes, config/secrets, RBAC, or networking — the same env-drift suspects.
