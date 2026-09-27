# Migration Strategies — The 7 Rs Framework

## What Is the 7 Rs Framework?

The **7 Rs** (sometimes called 6 Rs + Relocate) is AWS's standard taxonomy for classifying how on-premises workloads can be moved to or rationalized for the cloud. Every workload fits one of these strategies. The exam tests your ability to match a described scenario to the right R.

---

## The 7 Rs — Summary Table

| Strategy | Also Called | What You Do | AWS Service(s) | When to Use |
|---|---|---|---|---|
| **Retire** | Decommission | Turn it off — no business value, no migration needed | — | App is unused, redundant, or expired |
| **Retain** | Revisit later | Leave it on-prem for now | AWS Outposts (optional) | Not ready to migrate; regulatory; too complex currently |
| **Rehost** | Lift & Shift | Move as-is to EC2 with no changes | **AWS MGN** (Application Migration Service) | Fastest path; minimal change; speed matters |
| **Replatform** | Lift & Tinker | Minor optimizations without re-architecting | **DMS** (databases → RDS), containers on ECS | Move DB to managed RDS; move app to PaaS |
| **Repurchase** | Drop & Shop | Replace with a different SaaS or commercial product | — | GitHub/Confluence on-prem → SaaS |
| **Refactor** | Re-architect | Re-design to be cloud-native; biggest transformation | Multiple AWS services | Monolith → microservices; Oracle DB → Aurora |
| **Relocate** | Hypervisor lift | Move VMware or Kubernetes as-is to AWS | VMware Cloud on AWS, EKS Anywhere | On-prem VMware → VMware on AWS; on-prem K8s → EKS |

---

## Detailed Breakdown

### Retire
- The workload has no active users, no current business value, or is a duplicate of another system.
- Simply decommission it — no cloud spending needed.

### Retain
- Application is not yet ready to migrate (compliance, complexity, dependency).
- Keep running on-prem; revisit later.
- Can use **AWS Outposts** to host retained apps on AWS infrastructure physically in your datacenter.

### Rehost (Lift & Shift)
- Move servers to EC2 as-is — same OS, same application, same config.
- **AWS Application Migration Service (MGN)** automates this with continuous block-level replication.
- Fastest way to get off a datacenter; optimize later.
- **Example:** On-prem Windows Server → Amazon EC2 (same AMI-compatible OS).

### Replatform (Lift & Tinker)
- Small, targeted improvements without changing the core architecture.
- **Examples:**
  - On-prem MySQL → Amazon RDS for MySQL (managed, no OS to patch)
  - Windows app → Linux (OS swap)
  - App in a VM → containerized on ECS/EKS/Fargate
- Use **AWS DMS** for database replatforming.

### Repurchase (Drop & Shop)
- Replace the existing app with a commercial off-the-shelf or SaaS alternative.
- **Examples:**
  - On-prem Confluence → Confluence Cloud (SaaS)
  - On-prem GitHub → GitHub.com (SaaS)
  - On-prem CRM → Salesforce

### Refactor (Re-architect)
- The most complex strategy — rethink the architecture to fully leverage cloud-native capabilities.
- **Examples:**
  - Monolithic application → microservices on Lambda + API Gateway + DynamoDB
  - Oracle DB → Aurora PostgreSQL (with SCT + DMS)
  - Tightly-coupled batch job → event-driven with SQS + Lambda
- Highest long-term benefit; highest upfront effort.

### Relocate
- Move existing infrastructure to AWS without changing it — at the hypervisor or container layer.
- **Examples:**
  - On-prem VMware → VMware Cloud on AWS (same vSphere management console)
  - On-prem Kubernetes → Amazon EKS Anywhere

---

## Decision Tree

```
Is the app useful? 
  No → Retire
  
Is it ready/feasible to migrate?
  No → Retain
  
Do you want to move it as-is quickly?
  Yes → Rehost (MGN)
  
Do you want minor cloud optimizations (RDS, ECS)?
  Yes → Replatform (DMS)
  
Do you want to replace it with a SaaS product?
  Yes → Repurchase
  
Do you want to re-architect for cloud-native?
  Yes → Refactor

Are you moving VMware/Kubernetes infrastructure wholesale?
  Yes → Relocate
```

---

## Key Points / Exam Tips

- **Rehost = Lift & Shift = AWS MGN** — the fastest, no app changes.
- **Replatform = Lift & Tinker** — DB to RDS is the classic example; still the same app, just managed infra.
- **Refactor** involves the most risk, effort, and change — monolith → microservices is the classic example.
- **Relocate** is for VMware/Kubernetes at the hypervisor level — not for individual app servers.
- **Repurchase** means you're dumping the app entirely and buying a commercial replacement.
- **Retain** does NOT mean "migrate later to EC2" — it means leave on-prem.
- The exam loves the distinction between Rehost (no changes) and Replatform (minor optimizations).

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Move servers to EC2 as-is, no changes" | Rehost (Lift & Shift) — AWS MGN |
| "Move databases to managed RDS with minimal changes" | Replatform — AWS DMS |
| "Move VMware on-prem to VMware on AWS" | Relocate |
| "Replace on-prem tool with SaaS equivalent" | Repurchase |
| "Monolith → microservices, cloud-native" | Refactor |
| "No business value, decommission" | Retire |
| "Not ready to migrate yet" | Retain |
