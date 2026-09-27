# AWS Backup

## What Is AWS Backup?

**AWS Backup** is a fully managed, centralized backup service that automates and orchestrates backups across AWS services and on-premises resources from a single console. Instead of configuring backup settings independently in every service (RDS snapshots, EBS snapshots, EFS backup policies, etc.), AWS Backup provides one unified policy-driven approach.

---

## How It Works

```
Backup Plan (defines schedule + rules)
    |
    +--> Backup Rule 1: Daily backups, retain 30 days, move to cold storage after 7 days
    +--> Backup Rule 2: Weekly backups, retain 1 year, cross-region copy to us-west-2
    |
Resources assigned to the plan
    |
    +--> EC2 instances, EBS volumes, RDS databases, DynamoDB tables, EFS, FSx, S3, Storage Gateway
    |
Backups stored in a Backup Vault (isolated, encrypted storage)
```

---

## Core Components

| Component | Description |
|---|---|
| **Backup Plan** | Policy that defines: frequency, backup window, retention period, lifecycle, copy rules |
| **Backup Vault** | Secure, encrypted container that holds recovery points; different vaults for different retention needs |
| **Recovery Point** | A single backup snapshot — a restore point you can recover from |
| **Backup Rules** | One plan can have multiple rules (e.g., daily + weekly + monthly all in one plan) |

---

## Key Features

- **Centralized management** — one console, one policy framework for all supported services
- **Lifecycle management** — automatically move backups to cold (lower-cost) storage tiers after N days
- **Cross-Region copy** — replicate backups to another Region for regional disaster recovery
- **Cross-account copy** — copy backups to another AWS account (Organizations integration)
- **Incremental backups** — only the first backup is a full copy; subsequent backups are incremental
- **Encryption** — backups encrypted at rest using KMS keys
- **Audit Manager integration** — compliance reporting on backup activity

---

## Supported AWS Services

| Category | Services |
|---|---|
| Compute | EC2 (AMI backups), EBS volumes |
| Databases | RDS, Aurora, DynamoDB, DocumentDB, Neptune |
| File Storage | EFS, FSx (all variants) |
| Object Storage | Amazon S3 |
| Hybrid | AWS Storage Gateway (volumes) |

---

## Cross-Region and Cross-Account Backup

- **Cross-Region:** configure a **copy rule** in the backup plan specifying the destination Region. Useful for recovering from a complete Region outage.
- **Cross-account:** requires AWS Organizations. Backups copied to a separate account so that a compromised account cannot delete its own backups.

---

## AWS Backup vs Service-Native Snapshots

| | AWS Backup | Service-Native Snapshots (e.g., RDS manual snapshots) |
|---|---|---|
| Central management | Yes — single console | No — per-service consoles |
| Cross-service policy | Yes | No |
| Cross-region copy | Yes (built-in rule) | Manual for most services |
| Compliance reporting | Yes (Audit Manager) | Limited |
| Cost | Same underlying storage cost | Same underlying storage cost |

---

## Key Points / Exam Tips

- AWS Backup does NOT replace service-native backups for day-to-day operations — it orchestrates and centralizes them.
- Incremental backups: only the **first backup is a full copy**; all subsequent are incremental (only changed data).
- Use **cross-account backup** (via Organizations) to protect against accidental or malicious deletion — a rogue admin in the primary account cannot delete backups in a separate backup account.
- Use **Vault Lock** (WORM — Write Once Read Many) on a backup vault to prevent even admins from deleting recovery points during a retention period.
- Backup Plans can have multiple rules (daily, weekly, monthly) with different retention periods, all under one plan.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Centralized backup across multiple AWS services" | AWS Backup |
| "Automate backups with a single policy for EC2, RDS, EFS, DynamoDB" | AWS Backup |
| "Cross-region backup for disaster recovery" | AWS Backup cross-region copy rule |
| "Cross-account backup, protect against ransomware/admin deletion" | AWS Backup cross-account copy (via Organizations) |
| "Move old backups to cheaper storage automatically" | AWS Backup lifecycle (cold storage transition) |
| "WORM protection on backup vault" | AWS Backup Vault Lock |
