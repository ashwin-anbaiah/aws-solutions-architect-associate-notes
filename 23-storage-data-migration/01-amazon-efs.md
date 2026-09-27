# Amazon EFS (Elastic File System)

## What Is EFS?

**Amazon EFS** is a fully managed, serverless, elastic Network File System (NFS v4) that can be shared simultaneously by hundreds of EC2 instances, ECS tasks, EKS pods, Lambda functions, and SageMaker notebooks. You pay only for the storage you use — no provisioning required.

---

## Filesystem Redundancy Options

| Option | Storage | Durability | Cost |
|---|---|---|---|
| **Regional (Standard)** | Data replicated across 3+ AZs | 11 nines durability | Higher |
| **One Zone** | Data stored in a single AZ | Less resilient | ~50% cheaper than Regional |

- **Regional** is the default; suitable for production workloads.
- **One Zone** is ideal for non-critical or reproducible data (dev/test, scratch space).

---

## Storage Classes (Lifecycle Tiers)

| Class | Description | Lifecycle Policy Trigger |
|---|---|---|
| **EFS Standard** | SSD, sub-millisecond latency, active data | Always-on — no policy needed |
| **EFS Standard-IA** | Cost-optimized for data accessed a few times per quarter | Not accessed for **30 days** |
| **EFS Archive** | Long-lived data accessed a few times a year | Not accessed for **90 days** |

- Lifecycle policies automatically move files between tiers based on last-access time.
- "Transition to Standard" is set to **None** by default — files stay in IA/Archive.
- Lifecycle policy applies per file; each file is tracked individually.

---

## Performance Modes

| Mode | Latency | Best For |
|---|---|---|
| **General Purpose** (default) | Sub-millisecond | Web servers, CMS, home directories, small-scale workloads |
| **Max I/O** | Slightly higher per-operation | Highly parallelized — big data analytics, genomics, media processing, HPC with many concurrent clients |

> **Exam trap:** "Big data / many concurrent clients / parallel processing" always points to **Max I/O**. The throughput mode options (Bursting/Provisioned) are separate settings and are often distractors in performance-mode questions.

---

## Throughput Modes

| Mode | How It Works | Use When |
|---|---|---|
| **Bursting** | Throughput scales automatically with storage size | General-purpose workloads with variable demand |
| **Provisioned** | Set a fixed MiB/s independent of storage size | Workloads needing consistent throughput regardless of data volume |

---

## Mount Targets and Cross-AZ Access

- EFS exposes **mount targets** — one per Availability Zone — inside a VPC.
- All AZs in a Region can access the **same** EFS filesystem simultaneously through their respective mount targets.
- Instances in different AZs all mount the same filesystem; EFS handles replication internally.
- **Cross-Region access:** not natively a single unified filesystem. Options:
  - Inter-Region VPC Peering → connect EC2 in another Region to the mount target.
  - **EFS Replication** → async read-only replica for DR (separate filesystem, not live collaboration).
- **On-premises access:** connect over VPN or Direct Connect, then use an NFS client.

---

## Access Control

| Layer | Mechanism |
|---|---|
| Network | VPC Security Groups on mount targets |
| Identity | IAM policies (who can mount, access points) |
| File-level | POSIX permissions (owner/group/other, rwx) |

- NACLs are subnet-level — too coarse for EFS; not the right tool.
- EFS Access Points provide per-application root directory and enforced POSIX identity.

---

## Common Use Cases

- Containerized and serverless application shared storage (ECS + EFS is a standard pattern)
- ML training data shared across many GPU compute nodes
- Web content management (WordPress, Drupal) across multiple web servers
- User home directories for large teams

---

## Key Points / Exam Tips

- EFS is **Linux/NFS only** — Windows clients cannot natively mount EFS (use FSx for Windows File Server instead).
- **Multi-AZ by default** in Regional mode — all AZ mount targets are active simultaneously.
- **ECS/Fargate + EFS** = the standard "fully managed container + persistent shared storage" pattern.
- One Zone EFS is ~50% cheaper but single-AZ — choose only for re-creatable or non-critical data.
- Lifecycle thresholds: **30 days → Standard-IA**, **90 days → Archive**.
- Performance mode: Max I/O for parallel HPC; General Purpose for everything else.
- EFS is **not mountable natively by S3** — it is a file system, not object storage.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Shared storage for multiple Linux EC2 instances" | Amazon EFS |
| "Fully managed persistent storage for Fargate tasks" | Amazon EFS |
| "Big data / highly parallel workload on EFS" | Max I/O performance mode |
| "EFS data rarely accessed, reduce costs" | Enable lifecycle policy (IA/Archive tiers) |
| "On-prem NFS data to EFS" | DataSync agent → EFS mount target |
| "Many containers need the same files" | EFS (not EBS — EBS is single-instance) |
