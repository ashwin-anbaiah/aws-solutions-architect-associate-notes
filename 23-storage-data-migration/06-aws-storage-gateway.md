# AWS Storage Gateway

## What Is Storage Gateway?

**AWS Storage Gateway** is a hybrid storage service that connects on-premises applications to AWS cloud storage using familiar protocols (NFS, SMB, iSCSI). It allows on-premises workloads to seamlessly use S3, FSx, EBS, and Glacier — without requiring application changes.

The gateway appliance runs as a **virtual machine on-premises** (VMware, Hyper-V, KVM) or as a hardware appliance. It can also run on an EC2 instance in AWS for cloud-to-cloud scenarios.

---

## Gateway Types

### 1. Amazon S3 File Gateway

- **Protocol:** NFS or SMB
- **Backend storage:** Amazon S3
- On-premises apps read/write files as if it were a local NFS or SMB share.
- Files are stored as **S3 objects** — you can apply S3 lifecycle policies, replication, and encryption.
- A local cache retains recently accessed data for low-latency reads.
- **Use cases:** backup and archiving, log/image/video storage, content distribution to S3

### 2. Amazon FSx File Gateway

- **Protocol:** SMB
- **Backend storage:** FSx for Windows File Server
- Extends FSx for Windows to on-premises environments with a local cache for frequently accessed files.
- Supports **Active Directory** integration.
- **Use cases:** migrating Windows file workloads to AWS while maintaining on-prem access during transition

### 3. Volume Gateway

Provides **iSCSI block storage** volumes to on-premises servers.

| Mode | Primary Data | Backup in AWS | Local Access |
|---|---|---|---|
| **Cached Volumes** | Stored in S3 | Yes (EBS snapshots) | Hot/recent data cached locally |
| **Stored Volumes** | Full copy on-prem | Async backup to S3 as EBS snapshots | Full data, low latency always |

- **Cached Volumes:** minimize on-prem storage footprint; cloud is primary.
- **Stored Volumes:** on-prem is primary; cloud is DR copy. Maximum local performance.
- Both expose as iSCSI devices — compatible with any iSCSI initiator.

### 4. Tape Gateway

- **Protocol:** Virtual Tape Library (VTL) — iSCSI-compatible
- Presents virtual tapes to backup software (Veeam, Veritas, Commvault, etc.)
- Virtual tapes stored in S3; archived tapes moved to S3 Glacier or Glacier Deep Archive
- **Use case:** replace physical tape backup infrastructure with cloud-backed virtual tapes; no change to backup software configuration

---

## Storage Gateway Type Summary

| Type | Protocol | Backend | Best For |
|---|---|---|---|
| **S3 File Gateway** | NFS / SMB | Amazon S3 | On-prem apps storing files in S3 |
| **FSx File Gateway** | SMB | FSx for Windows | Windows workloads needing local cache to FSx |
| **Volume Gateway (Cached)** | iSCSI | S3 (primary) + EBS snapshots | Block storage, minimize on-prem hardware |
| **Volume Gateway (Stored)** | iSCSI | S3 (DR) — on-prem primary | Block storage, full local copy + async cloud backup |
| **Tape Gateway** | VTL / iSCSI | S3 Glacier / Glacier Deep Archive | Replace physical tape backup |

---

## Key Points / Exam Tips

- **S3 File Gateway** is the answer when: "keep using NFS/SMB on-prem" + "automated S3 tiering" + "minimal application changes."
- **Volume Gateway Stored** = on-prem is primary, cloud is backup. Full local dataset always available.
- **Volume Gateway Cached** = cloud is primary, only hot data cached locally. Smaller on-prem footprint.
- **Tape Gateway** = "replace physical tapes" — backup software sees virtual tapes, no config change needed.
- **FSx File Gateway** is used in hybrid Windows migrations: SMB local access with FSx for Windows as the backend.
- Storage Gateway is NOT for bulk data migration (use DataSync or Snow for that).
- The gateway appliance can be a VM on-prem or an EC2 instance; both behave identically.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Keep using NFS, minimal changes, hybrid, auto tiering to S3" | S3 File Gateway |
| "On-prem SMB access to FSx for Windows" | FSx File Gateway |
| "iSCSI block volumes with full local copy + cloud backup" | Volume Gateway — Stored Volumes |
| "iSCSI block volumes, minimize on-prem storage, cloud primary" | Volume Gateway — Cached Volumes |
| "Replace physical tape library, no backup software changes" | Tape Gateway |
| "On-prem backup to Glacier via virtual tape" | Tape Gateway |
