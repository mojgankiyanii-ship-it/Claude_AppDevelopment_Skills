# Cloud — AWS & Azure

Architect cloud the same way on either provider: identity-first, multi-account isolation, well-architected tradeoffs, cost-aware. The concepts map; the names differ.

## Contents
- Principles
- Service map (AWS ↔ Azure)
- Identity & access
- Account/subscription structure
- Networking
- Well-Architected lenses
- Cost
- Provider notes

---

## Principles

- **Identity over network position.** Least-privilege IAM, short-lived credentials, no long-lived keys. Most cloud breaches are misconfigured identity/storage, not exotic exploits.
- **Isolate blast radius** with multiple accounts/subscriptions (prod vs nonprod, per-workload), not one giant account.
- **Everything via IaC**; console for read/inspection, not changes.
- **Encrypt by default** (at rest + in transit); **private by default** (no public buckets/blobs or open security groups).
- **Design for the failure of a component/zone**; spread across AZs; know your region/AZ strategy.
- **Use managed services** to cut undifferentiated toil — until cost/lock-in/control makes self-managing worth it.

## Service map (AWS ↔ Azure)

| Capability | AWS | Azure |
|---|---|---|
| VMs | EC2 | Virtual Machines |
| Containers (managed K8s) | EKS | AKS |
| Serverless containers | ECS/Fargate | Container Apps / ACI |
| Functions | Lambda | Azure Functions |
| Object storage | S3 | Blob Storage |
| Managed SQL | RDS / Aurora | Azure SQL / DB for PostgreSQL/MySQL |
| NoSQL | DynamoDB | Cosmos DB |
| Networking | VPC | VNet |
| Load balancer | ALB/NLB | Load Balancer / App Gateway |
| Identity | IAM | Entra ID + RBAC |
| Secrets | Secrets Manager / SSM | Key Vault |
| IaC-native | CloudFormation/CDK | ARM/Bicep |
| Messaging/queue | SQS/SNS/EventBridge | Service Bus / Event Grid |
| Observability | CloudWatch / X-Ray | Monitor / App Insights |
| Org structure | Organizations / Control Tower | Management Groups / Landing Zones |

(Terraform/OpenTofu works across both and is the common multi-cloud IaC default.)

## Identity & access

- **Least privilege**, scoped roles; avoid wildcard `*` permissions and broad admin.
- **No long-lived static keys.** Humans use SSO/federation; workloads use instance/pod identity (IAM Roles for Service Accounts on EKS, Managed Identities on Azure); CI uses **OIDC** to assume short-lived roles.
- Separate roles per environment; require MFA; review access regularly; log all API calls (CloudTrail / Azure Activity Log) and alert on sensitive actions.

## Account / subscription structure

- **AWS:** Organizations with separate accounts per environment/workload; Control Tower / landing zone; Service Control Policies as org-wide guardrails; centralized logging and billing.
- **Azure:** Management Groups → Subscriptions → Resource Groups; Azure Policy for guardrails; landing zones (CAF) for a governed baseline.
- Centralize identity, logging, networking, and security tooling; isolate workloads. Guardrails (SCP/Azure Policy) prevent whole classes of mistakes org-wide.

## Networking

- Private subnets for workloads, public only for ingress (LB/NAT). Security groups/NSGs least-privilege; default-deny then allow.
- Private connectivity to managed services (VPC/Private Endpoints) instead of public internet where possible.
- Plan CIDRs to avoid overlap (it blocks future peering/VPN). Use peering/Transit Gateway / VNet peering + hub-spoke for multi-network.
- Egress control and DNS are common failure points — design and monitor them.

## Well-Architected lenses

Evaluate designs against these (AWS and Azure publish equivalents):
- **Operational excellence** — automation, observability, runbooks, change safety.
- **Security** — identity, data protection, detection, least privilege.
- **Reliability** — multi-AZ, failover, backups/DR with real RTO/RPO, graceful degradation.
- **Performance efficiency** — right service/size, scale elastically, measure.
- **Cost optimization** — right-size, purchase models, kill waste.
- **Sustainability** — efficient resource use.

Use them as a checklist for tradeoffs, not dogma.

## Cost

- **Tag everything** (owner, env, cost-center) and use cost allocation; you can't optimize what you can't attribute.
- **Right-size** from real metrics; turn off nonprod off-hours; delete orphans (unattached disks, idle LBs, old snapshots).
- **Commit** for steady-state (Savings Plans/Reserved/Azure Reservations); **spot/low-priority** for fault-tolerant/batch.
- Set **budgets and anomaly alerts**; review the bill monthly. Storage tiering and data-egress are sneaky cost drivers — egress especially.
- Cost is an architecture property — design for it, don't bolt it on after a surprise invoice.

## Provider notes

- **AWS** — broadest service set; IAM is powerful and easy to over-grant (be deliberate). S3 public-access and security-group 0.0.0.0/0 are classic mistakes — block them with org guardrails.
- **Azure** — tight Entra ID / Microsoft ecosystem integration; **Bicep** is the pleasant native IaC over raw ARM; RBAC + Azure Policy for governance; resource-group lifecycle is a useful boundary.
- **Multi-cloud** is real cost and complexity — do it for genuine reasons (resilience, regulation, acquisitions), not buzz. Keep a portable core (containers, Terraform) and accept some provider-specific edges.
