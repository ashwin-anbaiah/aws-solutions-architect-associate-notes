# AWS DataSync

## What Is DataSync?

**AWS DataSync** is a fully managed, automated data transfer service that moves data between on-premises storage systems and AWS storage services — or between AWS storage services. It handles scheduling, monitoring, retry on failure, data integrity verification, and bandwidth throttling.

---

## How DataSync Works

```
On-premises NAS/NFS/SMB/HDFS
        |
   DataSync Agent (VM on-premises)
        |  (TLS encrypted)
   AWS DataSync Service
        |
   Target: S3 / EFS / FSx
```

- A lightweight **DataSync agent** (virtual machine) is deployed on-premises.
- The agent connects to the DataSync service in AWS over the internet, VPN, or Direct Connect.
- You define **tasks** (source location → destination location) and schedule them.

---

## Supported Sources and Destinations

| Source | Destination |
|---|---|
| On-premises NFS shares | Amazon S3 (any storage class) |
| On-premises SMB shares | Amazon EFS |
| Hadoop HDFS | Amazon FSx for Windows |
| AWS Snowcone (pre-installed agent) | Amazon FSx for Lustre |
| Amazon S3 on Outposts | Amazon FSx for NetApp ONTAP |
| Other cloud providers (Google, Azure) | Amazon FSx for OpenZFS |

---

## Key Features

- **Scheduled transfers:** hourly, daily, weekly, or custom — not just one-time
- **Incremental sync:** only changed files/data transferred after the initial full copy
- **Bandwidth throttling:** set a limit (single task can use up to 10 Gbps)
- **Parallel tasks:** run multiple tasks simultaneously for different folders/paths
- **TLS encryption** in transit
- **Data integrity verification** — checksums validated at source and destination
- **CloudWatch integration** — monitor transfer metrics, errors, and progress
- **Failure handling** — automatically retries failed transfers

---

## Network Connectivity Options

| Method | Notes |
|---|---|
| Over the internet (TLS) | Public DataSync endpoint; simplest setup |
| Over Site-to-Site VPN | IPsec security over internet + VPC endpoint |
| Direct Connect – Public VIF | High-bandwidth dedicated; uses public DataSync endpoint |
| Direct Connect – Private VIF | Routes through VPC endpoint into private network |

> **Exam tip:** "On-prem NFS → EFS via Direct Connect with least ops overhead" → DataSync agent + **Private VIF** + **PrivateLink interface VPC endpoint for EFS**.

---

## DataSync vs Transfer Family

| Dimension | AWS DataSync | AWS Transfer Family |
|---|---|---|
| Primary purpose | Bulk/scheduled data migration and synchronization | Interactive file transfers by end users or partner systems |
| Initiated by | System/scheduler (automated) | Human users or legacy application |
| Protocols | NFS, SMB, HDFS (agent-based) | SFTP, FTPS, FTP, AS2 |
| Destinations | S3, EFS, FSx family | S3 and EFS only |
| User auth/access control | Not applicable (system-to-system) | Central user auth (IAM, AD, LDAP, Cognito) |
| Typical use | One-time or recurring automated migration | Replacing FTP servers, B2B file exchange |

---

## Key Points / Exam Tips

- DataSync is for **system-to-system, automated, scheduled bulk transfers** — not for human users doing interactive uploads.
- Transfer Family is for **user-facing, interactive file transfers** using traditional protocols (SFTP/FTP/FTPS).
- DataSync supports **all FSx variants** as a destination; Transfer Family only supports S3 and EFS.
- A single DataSync task supports up to **10 Gbps**; run multiple tasks in parallel for more throughput.
- DataSync supports **multiple cloud providers** as source (GCS, Azure Blob).
- DataSync does NOT require an agent for AWS-to-AWS transfers (e.g., S3 bucket to EFS).

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Automate/schedule regular data sync from on-prem to S3/EFS/FSx" | AWS DataSync |
| "Large-scale one-time migration from NAS to S3" | AWS DataSync |
| "Move data between AWS storage services automatically" | AWS DataSync |
| "Bandwidth-limited migration, need throttling" | AWS DataSync (bandwidth limit per task) |
| "On-prem HDFS to AWS storage" | AWS DataSync (supports HDFS) |
| "S3/EFS/FSx as migration destination" | AWS DataSync |
