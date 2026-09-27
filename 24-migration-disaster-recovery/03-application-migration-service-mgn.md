# AWS Application Migration Service (MGN)

## What Is MGN?

**AWS Application Migration Service (MGN)** is the primary AWS service for **Rehost (Lift & Shift)** migrations. It continuously replicates on-premises or cloud servers at the block level to AWS, then launches them as EC2 instances during cutover. MGN minimizes downtime and data loss by keeping the source and target in near-constant sync until you're ready to cut over.

> MGN replaced the older AWS Server Migration Service (SMS). If you see SMS in exam prep materials, treat it as MGN.

---

## How MGN Works

```
Step 1: Install Replication Agent on source server (on-prem VM or physical server)
            ↓
Step 2: Agent performs continuous block-level replication
        (compressed + encrypted in transit)
            ↓
Step 3: Replicated data lands in a staging area
        (AWS-managed EC2 replication servers + EBS volumes)
            ↓
Step 4: Run Test Migration — launch a non-production EC2 instance, validate app works
            ↓
Step 5: Run Cutover Migration — redirect production traffic to new EC2 instance
            ↓
Step 6: Decommission the source server
```

---

## Key Technical Details

- **Replication type:** Continuous block-level replication (not file-level, not snapshot-based)
- **Replication agent:** Must be installed on each source VM or physical server
- **Staging area:** AWS-managed temporary EC2 replication servers + EBS volumes in the target Region
- **Encryption:** Data compressed and encrypted in transit
- **RPO (Recovery Point Objective):** Near-zero — continuous replication means minimal data loss
- **RTO (Recovery Time Objective):** Near-zero downtime during cutover — source and target stay in sync up to the moment of switch
- **OS support:** Any OS compatible with EC2 (Linux, Windows, etc.)
- **Source environments:** On-premises, other cloud providers (Azure, GCP)

---

## Test Migration vs Cutover Migration

| | Test Migration | Cutover Migration |
|---|---|---|
| Purpose | Validate the migrated instance works correctly before production cutover | The actual production go-live |
| Impact on source | None — source keeps running, users unaffected | Source is decommissioned after cutover |
| Environment | Test environment (non-production) | Production |
| Recommended | Yes — run test migrations multiple times | Once, after testing is done |

---

## MGN vs DMS — Key Differences

| Dimension | AWS MGN | AWS DMS |
|---|---|---|
| What it migrates | Application servers (entire OS + application) | Databases |
| How it replicates | Block-level (entire disk) | Row-level (database transactions) |
| Migration type | Rehost (Lift & Shift) — like-to-like | Replatform — can change DB engine |
| Supports heterogeneous? | No (same OS type) | Yes (with SCT: Oracle → Aurora PostgreSQL) |
| Target | Amazon EC2 | RDS, Aurora, Redshift, DynamoDB |

---

## MGN for AWS-to-AWS Cross-Region Migration

- MGN also supports **cross-region migrations** within AWS.
- Useful when you need to move workloads from one AWS Region to another (e.g., eu-west-1 → us-east-1).
- Same block-level replication approach; replication agent still needed.

---

## Key Points / Exam Tips

- MGN = Rehost = Lift & Shift = minimal changes = fastest path to cloud.
- **Continuous block-level replication** = near-zero RPO and near-zero RTO during cutover.
- **Always run Test Migration before Cutover** — this is the recommended best practice.
- MGN handles servers, NOT databases — use DMS for database migration.
- The replication agent must be manually installed on each source server.
- MGN reduces downtime to near-zero because source and target remain in sync until the last moment.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Lift-and-shift servers to EC2, minimal downtime" | AWS MGN |
| "Block-level replication of on-prem servers to AWS" | AWS MGN |
| "Continuous replication, near-zero RPO for server migration" | AWS MGN |
| "Test migration before cutover" | AWS MGN (test migration feature) |
| "Move entire server (OS + app) to EC2, no changes" | AWS MGN |
