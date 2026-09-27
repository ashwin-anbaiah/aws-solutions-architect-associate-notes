# Amazon RDS

## What Is RDS?

- **Amazon Relational Database Service (RDS)** — fully managed SQL database service.
- AWS handles: provisioning, patching, upgrades, backup, recovery, repair, and monitoring.
- Supported engines:
  - **Open-source:** MySQL, PostgreSQL, MariaDB
  - **Commercial:** Oracle, SQL Server, IBM DB2
  - **AWS-native:** Amazon Aurora

## RDS Multi-AZ (High Availability)

- **Primary DB** in one AZ; **Standby DB** in another AZ.
- Application reads/writes to Primary only.
- Data is **synchronously** replicated from Primary to Standby.
- On Primary failure or AZ failure: DNS endpoint automatically points to Standby (failover in 60–120 sec).
- **HA solution — not a scaling solution.** The standby cannot serve read traffic.

## RDS Read Replicas (Read Scaling)

- Create up to **15 Read Replicas** in the same or different AWS region.
- Data is **asynchronously** replicated from Primary to replicas.
- Applications can read from any replica to reduce load on Primary.
- **Cross-region** read replicas: DTO charges apply.
- Can **promote** a read replica to a standalone DB for disaster recovery.
- Use cases: business reporting, data warehousing, low-latency reads from a remote region.

## RDS vs Multi-AZ vs Read Replica

| Feature | Multi-AZ | Read Replica |
|---|---|---|
| Purpose | High Availability / failover | Read scaling |
| Replication | Synchronous | Asynchronous |
| Readable during normal operation | No (standby is passive) | Yes |
| Cross-region support | Yes (Multi-AZ Cluster) | Yes |
| Failover | Automatic | Manual promotion |

## RDS Storage Auto Scaling

- Automatically increases storage when needed.
- Triggered when ALL of these conditions are met:
  1. Free storage < 10% of allocated
  2. Low-storage condition persists ≥ 5 minutes
  3. ≥ 6 hours since last storage increase
- Requires setting a **Maximum Storage Threshold**.
- Supported on gp2, gp3, and Provisioned IOPS volumes (not Magnetic).

## RDS Custom

- Gives SSH/RDP access to the underlying host — unlike standard RDS.
- Supports **Oracle** and **SQL Server** only.
- Use for: legacy apps needing OS-level changes, custom patches/agents, or special configurations.
- Best practice: **disable Automation Mode** before making customizations; take a DB snapshot first.

## RDS Backup

| Type | Retention | Notes |
|---|---|---|
| **Automated (Continuous)** | 1–35 days | Point-in-time restore (PITR) with 1-second granularity; stored in S3 |
| **Manual Snapshot** | Indefinite | On-demand; can be copied across accounts and regions |

- Backups occur during a configurable daily backup window.
- Restoring a snapshot always creates a **new DB instance**.

## RDS Security

- **Authentication:** Username/password (all engines); IAM token auth (MySQL and PostgreSQL only).
- **Credentials storage:** Use **AWS Secrets Manager** or **SSM Parameter Store**.
- **Encryption at rest:** AWS KMS; must be enabled at creation time (cannot be added later to an existing unencrypted DB).
- **Encryption in transit:** TLS/SSL.
- **Network:** Full VPC isolation — subnets, security groups, NACLs.
- **To encrypt an existing unencrypted RDS DB:** Snapshot → copy snapshot with encryption enabled → restore new encrypted DB instance.

## RDS Reserved Instances

- Save up to **69%** vs On-Demand for steady-state workloads.
- Options: All Upfront, Partial Upfront, No Upfront.
- Terms: 1-year or 3-year (No Upfront is 1-year only).

---

## Key Points / Exam Tips

- **Trigger:** "managed relational DB, MySQL/PostgreSQL/Oracle/SQL Server" → **Amazon RDS**
- **Trigger:** "high availability for RDS, automatic failover" → **RDS Multi-AZ**
- **Trigger:** "scale read queries, reduce DB load" → **RDS Read Replicas**
- **Trigger:** "encrypt existing unencrypted RDS" → snapshot → encrypted copy → restore (cannot enable in-place)
- Multi-AZ standby is **passive** — cannot serve reads; it is only for failover
- RDS read replicas use **asynchronous** replication — there may be slight lag (replication lag)
- IAM authentication for RDS is only supported on **MySQL and PostgreSQL**
- **Trigger:** "need OS access to the RDS host" → **RDS Custom** (Oracle/SQL Server only)
