# Infrastructure as Code

Provision infrastructure reproducibly, reviewably, and safely. The plan is the contract; read it every time.

## Contents
- Principles
- Terraform: state
- Terraform: structure & modules
- Environments
- Drift & imports
- Policy as code
- Other tools
- Safety rules

---

## Principles

- **Declarative desired state** in version control, peer-reviewed via PRs. The repo is the source of truth; the cloud console is read-only in practice.
- **Idempotent and reproducible** — applying the same config converges to the same result. No manual drift.
- **Plan before apply, always.** The plan is a precise diff of what will change; review it like a code change, especially for replacements/deletes.
- **Modular and DRY** — reusable modules with clear inputs/outputs; compose rather than copy-paste.
- **Small, frequent changes** over big-bang applies — smaller blast radius, easier review and rollback.

## Terraform: state

- **Remote backend** (S3+DynamoDB lock, Azure Storage, Terraform Cloud, GCS) — never local state for shared/prod infra.
- **State locking** to prevent concurrent corrupting applies. **Encrypt** state (it can contain secrets). Restrict access tightly.
- **Isolate state by environment and blast radius** — separate state files for prod vs nonprod, and split large estates (network / data / app) so one apply can't nuke everything. Smaller states = smaller blast radius and faster plans.
- Never hand-edit state. Use `terraform state` subcommands, `import`, and `moved` blocks.

## Terraform: structure & modules

- **Root configs per environment/stack** that wire together reusable **modules**. Modules encapsulate a capability (a VPC, an EKS cluster, a service) with typed variables and outputs.
- Pin **provider and module versions**; pin the Terraform version. Unpinned versions cause surprise diffs.
- Keep modules focused and composable; avoid mega-modules with 100 toggles. Document inputs/outputs.
- Name and tag everything consistently (owner, env, cost-center) — tags drive cost, security, and ops.
- Keep secrets out of code/state where possible; source from a secret manager at apply/runtime.

## Environments

- Promote the **same module code** across environments with different variable values (sizes, counts, flags) — parity reduces "works in staging" surprises.
- Prefer **separate state per environment** (directories or Terraform workspaces) with isolated credentials. Be cautious with workspaces for strong prod isolation; many teams prefer separate root configs + backends.
- Lower environments mirror prod's shape (not its scale) so changes are validated realistically.

## Drift & imports

- **Drift** = reality diverged from code (someone click-opped). Detect with scheduled `plan`; reconcile by codifying the change or reverting it. Make click-ops the exception that gets codified, not the norm.
- **Import** existing resources into state to bring them under management (`import` blocks / `terraform import`) rather than recreating.
- **`moved` blocks** to refactor/rename without destroy-recreate. Review plans for unintended replacements.

## Policy as code

- Guardrails enforced automatically: **OPA/Conftest, Sentinel, tfsec/checkov/tflint** in CI on every plan.
- Enforce: encryption on, no public buckets/SGs open to 0.0.0.0/0, required tags, allowed regions/instance types, no hardcoded secrets.
- Shift security left — fail the PR, don't discover it in prod.

## Other tools

- **Pulumi / AWS CDK / CDKTF** — IaC in real languages (TS/Python/Go); good for complex logic and teams that prefer code over HCL. Same discipline applies (state, plan/preview, modules).
- **Ansible** — configuration management and provisioning of mutable systems / OS-level config; agentless. Pairs with image-baking (Packer) for golden images. Prefer immutable infra over heavy config management where you can.
- **Crossplane** — provision cloud infra via Kubernetes APIs/GitOps when you're K8s-centric.

## Safety rules (hard-won)

1. **Read every plan** before apply — scan for `destroy` and `replace` on stateful resources (databases, volumes, buckets). That diff is where prod dies.
2. **Protect stateful resources** with `prevent_destroy` lifecycle and deletion protection; take backups before risky applies.
3. **Never auto-apply unreviewed plans to prod.** Plan in CI, gate apply behind review/approval.
4. **Isolate blast radius** with small states and least-privilege apply credentials per stack.
5. **Tag and document** so the next person (you, in six months) understands ownership and intent.
