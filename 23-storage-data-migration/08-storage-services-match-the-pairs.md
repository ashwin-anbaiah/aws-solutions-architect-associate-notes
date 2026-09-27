# Storage Services — Match the Pairs (Comprehensive Comparison)

This file provides a single reference table covering ALL AWS storage services and how to select the right one for any given scenario on the SAA-C03 exam.

---

## Master Comparison Table

| Service | Type | Protocol | OS/Client | Multi-Instance? | Managed? | Best For | Key Differentiator |
|---|---|---|---|---|---|---|---|
| **Amazon S3** | Object | HTTPS (API/SDK) | Any | Yes (API-based) | Yes | Static files, backups, data lake, websites | No file system; unlimited scale; cheapest per GB at scale |
| **Amazon EBS** | Block | N/A (attached) | Linux/Windows | No (single EC2) | Yes | Boot volumes, DB data, low-latency block I/O | Single-AZ, single-instance; highest consistent IOPS |
| **Instance Store** | Block (ephemeral) | N/A (attached) | Linux/Windows | No | N/A | Temp HPC scratch, buffer, cache | Fastest raw I/O; lost on stop/terminate |
| **Amazon EFS** | File (NFS) | NFS v4 | Linux only | Yes (hundreds) | Yes | Shared Linux storage for EC2/containers | Multi-AZ; elastic; concurrent multi-instance |
| **FSx for Windows** | File (SMB) | SMB / NTFS | Windows + Linux | Yes | Yes | Windows workloads, AD-integrated file shares | AD + DFS + NTFS — only FSx with full Windows stack |
| **FSx for Lustre** | File (HPC) | Lustre | Linux (POSIX) | Yes (thousands) | Yes | HPC, ML training, genomics, rendering | Extreme parallel throughput + native S3 integration |
| **FSx for NetApp ONTAP** | File (multi-protocol) | NFS + SMB + iSCSI | Linux + Windows | Yes | Yes | On-prem NetApp lift-and-shift | Dedup, compression, multi-protocol simultaneous |
| **FSx for OpenZFS** | File (NFS) | NFS | Linux | Yes | Yes | On-prem ZFS lift-and-shift | ZFS snapshots, clones, high IOPS |
| **S3 File Gateway** | Hybrid (file→S3) | NFS / SMB | Linux + Windows | Yes | Yes | On-prem apps storing in S3 | Local cache; on-prem NFS/SMB; S3 as backend |
| **FSx File Gateway** | Hybrid (SMB→FSx) | SMB | Windows | Yes | Yes | On-prem SMB access to FSx for Windows | Local cache; migration bridge |
| **Volume GW (Cached)** | Hybrid (block) | iSCSI | Any (iSCSI) | No | Yes | On-prem block storage; minimize local hardware | Primary data in S3; hot data cached locally |
| **Volume GW (Stored)** | Hybrid (block) | iSCSI | Any (iSCSI) | No | Yes | Full local copy + async cloud backup | Primary data on-prem; S3 = DR copy only |
| **Tape Gateway** | Hybrid (virtual tape) | VTL (iSCSI) | Backup software | No | Yes | Replace physical tapes | No backup software changes; tapes to Glacier |
| **AWS Snowcone** | Edge/offline | USB/NFS | Any | N/A | N/A | Small-scale remote/disconnected sites | 8–14 TB; lightest; DataSync agent built in |
| **Snowball Edge Storage** | Edge/offline | NFS/S3 | Any | N/A | N/A | Large-scale offline migration | Up to 80 TB HDD / 210 TB SSD per device |
| **Snowball Edge Compute** | Edge + compute | NFS/S3 + EC2 | Any | N/A | N/A | Edge ML, video analytics | EC2 workloads + storage at remote sites |
| **Snowmobile** | Offline (truck) | N/A (physical) | N/A | N/A | N/A | Exabyte-scale datacenter migration | 100 PB per truck |
| **AWS DataSync** | Online transfer | NFS/SMB/HDFS | Any | N/A | Yes | Automated bulk migration + ongoing sync | Scheduled, incremental, supports all FSx variants |
| **AWS Transfer Family** | Managed endpoints | SFTP/FTP/FTPS/AS2 | Any | N/A | Yes | Legacy file transfer protocols | Human/partner-driven; replaces FTP servers |
| **AWS Backup** | Backup orchestration | N/A (centralized) | N/A | N/A | Yes | Cross-service centralized backup policy | One policy for EC2, RDS, EFS, DynamoDB, FSx, S3 |

---

## Scenario → Service Quick-Pick

| Scenario | Right Service |
|---|---|
| Host a static website | **S3** |
| Store relational database files (MySQL, PostgreSQL) | **EBS** (gp3 or io2) |
| High-performance scratch/temp storage for HPC nodes | **Instance Store** or **FSx for Lustre (Scratch)** |
| Shared storage for 50 Linux EC2 web servers | **EFS** |
| Shared storage for 50 Windows EC2 app servers | **FSx for Windows File Server** |
| ML training on S3 datasets, thousands of GPU nodes | **FSx for Lustre** |
| On-prem NetApp → AWS, keep all features | **FSx for NetApp ONTAP** |
| On-prem ZFS → AWS | **FSx for OpenZFS** |
| On-prem apps still use NFS, store data in S3 | **S3 File Gateway** |
| On-prem Windows users need SMB access to FSx | **FSx File Gateway** |
| On-prem backup server stores backups to cloud block volumes | **Volume Gateway (Stored or Cached)** |
| Replace physical tape library, no software changes | **Tape Gateway** |
| Move 500 TB offline, bandwidth too slow | **Snowball Edge Storage Optimized** |
| Move 50 PB entire datacenter | **Snowmobile** |
| IoT sensor data collection in a factory | **Snowcone** |
| Automate nightly sync from on-prem NAS to S3 | **DataSync** |
| Vendors upload via SFTP into S3, fully managed | **Transfer Family** |
| Centralize automated backups for RDS + EFS + EC2 | **AWS Backup** |
| Backup data to another Region automatically | **AWS Backup (cross-region copy rule)** |
| Backup to another AWS account (ransomware protection) | **AWS Backup (cross-account copy)** |

---

## Protocol Reference

| Protocol | Services That Use It |
|---|---|
| **NFS** | EFS, FSx for OpenZFS, S3 File Gateway, DataSync (source) |
| **SMB** | FSx for Windows, FSx File Gateway, FSx for NetApp ONTAP |
| **iSCSI** | Volume Gateway (Cached/Stored), Tape Gateway, FSx for NetApp ONTAP |
| **Lustre** | FSx for Lustre |
| **SFTP / FTP / FTPS / AS2** | AWS Transfer Family |
| **HTTPS (API)** | Amazon S3 |

---

## Key Decision Rules (Exam Shortcuts)

1. **"Linux shared file system"** → EFS (NFS, no AD needed) or FSx for Lustre (HPC + S3).
2. **"Windows shared file system"** → FSx for Windows File Server (SMB + AD + NTFS).
3. **"Hybrid — keep using NFS on-prem"** → S3 File Gateway (not EFS, which requires full migration).
4. **"Replace physical tape"** → Tape Gateway (backup software unchanged).
5. **"Legacy SFTP users"** → AWS Transfer Family (not DataSync — that's automated/system-to-system).
6. **"Automated scheduled bulk sync"** → DataSync (not Transfer Family).
7. **"Offline TBs–PBs migration"** → Snowball Edge (not DataSync — that's online).
8. **"Centralized multi-service backup policy"** → AWS Backup (not service-native snapshots).
9. **"S3 + on-prem access, no app changes"** → S3 File Gateway.
10. **"EC2 single-instance block storage"** → EBS (not EFS — EFS is file, multi-instance).
