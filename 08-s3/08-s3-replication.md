# S3 Replication (CRR and SRR)

## What is S3 Replication?

**S3 Replication** automatically and asynchronously copies objects from a **source bucket** to one or more **destination buckets**. Replication happens in the background — new objects are replicated after they are uploaded to the source.

---

## Replication Types

| Type | Abbreviation | Scope | Common Use Cases |
|---|---|---|---|
| **Cross-Region Replication** | CRR | Source and destination in different AWS regions | Lower latency for global users, DR, compliance (data sovereignty) |
| **Same-Region Replication** | SRR | Source and destination in the same region | Log aggregation, test/prod environment sync, account separation |

---

## Prerequisites

Both are required before setting up replication:

1. **Versioning must be enabled** on both source and destination buckets
2. Appropriate **IAM role** with permissions to read from source and write to destination

---

## Key Replication Behaviors

| Behavior | Detail |
|---|---|
| **New objects only (by default)** | Replication applies to new uploads after the rule is enabled |
| **Existing objects** | Use **S3 Batch Replication** to replicate objects that existed before the rule was enabled |
| **Encryption** | Encrypted objects (SSE-KMS) can be replicated — requires KMS key permissions |
| **Prefix/tag filters** | You can narrow replication scope to specific prefixes or object tags |
| **Chaining** | Not supported: A → B → C does NOT automatically replicate A → C |
| **Cross-account** | Destination bucket can be in a different AWS account |
| **Data transfer cost** | CRR incurs cross-region data transfer charges; SRR does not |
| **CloudWatch monitoring** | Metrics: bytes/operations pending, replication latency, failed operations |

---

## Replication Time Control (RTC)

**S3 Replication Time Control (RTC)** provides a guaranteed **15-minute SLA** for replication of 99.99% of objects.

- Enabled per replication rule (optional add-on)
- Provides CloudWatch metrics to monitor replication lag
- Additional cost beyond standard replication
- Use when business requirements demand near-real-time data availability in the destination

---

## How Deletes Affect Replication

| Delete Scenario | Default Behavior | With Delete Marker Replication Enabled |
|---|---|---|
| Delete without Version ID (adds delete marker) | Delete marker is **NOT replicated** | Delete marker IS replicated to destination |
| Delete with Version ID (permanent version delete) | **NOT replicated** (protects against malicious deletion) | Still NOT replicated |

**Key insight:** Permanent deletes using a Version ID are never replicated — this is a safety feature to prevent accidental or malicious deletions from propagating to your backup copy.

---

## SRR vs CRR Comparison

| Feature | SRR | CRR |
|---|---|---|
| Regions | Same region | Different regions |
| Cost | Storage cost only | Storage + data transfer cost |
| Use for DR | No (same region failure affects both) | Yes (different region = separate blast radius) |
| Compliance | Account/environment separation | Data sovereignty requirements |
| Latency benefit | No geographic benefit | Users closer to destination region get faster access |

---

## Key Points / Exam Tips

- **Versioning required** on both buckets — this is a hard prerequisite
- Existing objects are NOT replicated automatically — use **S3 Batch Replication** for existing objects
- Delete marker replication is **opt-in** — disabled by default
- Permanent version-ID deletions are **never replicated** (intentional anti-ransomware protection)
- Replication chaining (A→B→C) is not supported — set up a direct A→C rule separately
- For SSE-KMS objects, the IAM replication role needs appropriate KMS key permissions
- **RTC** (15-min SLA) is a paid add-on — choose it when business RPO requires near-real-time replication

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Replicate S3 data to another region for disaster recovery" | CRR (Cross-Region Replication) |
| "Aggregate logs from multiple accounts into one bucket" | SRR (Same-Region Replication) |
| "Replicate data from production to test account" | SRR |
| "Existing objects not being replicated" | Use S3 Batch Replication for pre-existing objects |
| "Guarantee replication within 15 minutes" | S3 Replication Time Control (RTC) |
| "Delete not replicated to backup bucket" | Default behavior — delete markers not replicated; permanent deletes never replicated |
| "Prevent malicious deletion from propagating to backup" | S3 Replication (version-ID deletes not replicated by design) |
