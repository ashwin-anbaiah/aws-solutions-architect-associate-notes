# Amazon FSx

## What Is FSx?

**Amazon FSx** provides fully managed, purpose-built third-party file systems on AWS. Unlike EFS (generic NFS), each FSx flavor replicates a well-known on-premises file system with its own protocol, feature set, and performance profile — enabling lift-and-shift migrations with minimal application changes.

---

## FSx Family Overview

| Service | Protocol(s) | Primary Use Case |
|---|---|---|
| **FSx for Windows File Server** | SMB / NTFS | Windows workloads, AD-integrated shared drives |
| **FSx for Lustre** | Lustre (POSIX) | HPC, ML training, video rendering, genomics |
| **FSx for NetApp ONTAP** | NFS / SMB / iSCSI | Lift-and-shift from on-prem NetApp appliances |
| **FSx for OpenZFS** | NFS | Lift-and-shift from on-prem ZFS systems |

---

## FSx for Windows File Server

- Fully managed Windows shared file system powered by **Windows File Server**
- Supports **SMB protocol** and **NTFS** permissions natively
- Natively integrates with **Microsoft Active Directory** (on-prem AD or AWS Managed Microsoft AD)
- Supports **DFS (Distributed File System)** namespaces — organize shares across hundreds of PB
- Accessible across VPCs, Regions, and accounts via VPC peering or Direct Connect
- Accessible from on-premises servers over VPN or Direct Connect
- Automated daily backups to S3; supports Windows Volume Shadow Copies (VSS)

**Key features for exam:** SMB + AD + NTFS + DFS = FSx for Windows. No other AWS storage service checks all four boxes.

---

## FSx for Lustre

- High-performance parallel file system designed for HPC and data-intensive workloads
- Scales to **hundreds of GB/s** of aggregate throughput and millions of IOPS
- **Native S3 integration:** S3 objects appear as files in the Lustre file system; processed output writes back to S3 automatically

### Deployment Types

| Type | Durability | Cost | Use When |
|---|---|---|---|
| **Scratch** | No replication; data lost on hardware failure | Lower | Temporary processing, short jobs, cost-sensitive |
| **Persistent** | Data replicated within the AZ | Higher | Long-running jobs requiring data durability |

**Use cases:** ML training data from S3, CFD simulations, VFX/rendering, genomics, chip design.

---

## FSx for NetApp ONTAP

- Fully managed ONTAP file system supporting **NFS, SMB, and iSCSI simultaneously**
- Enterprise features: data deduplication, compression, instant cloning, ONTAP snapshots
- Multi-protocol access: same data readable via NFS from Linux AND via SMB from Windows
- **Ideal for:** migrating on-premises NetApp environments to AWS with minimal changes

---

## FSx for OpenZFS

- Fully managed ZFS file system exposed over **NFS**
- High IOPS, low latency (sub-millisecond); supports ZFS snapshots and clones
- **Ideal for:** migrating on-premises ZFS-based storage to AWS

---

## EFS vs FSx for Windows vs FSx for Lustre

| Dimension | EFS | FSx for Windows | FSx for Lustre |
|---|---|---|---|
| Protocol | NFS (Linux) | SMB (Windows/Linux) | Lustre (Linux/POSIX) |
| AD Integration | No | Yes | No |
| DFS Support | No | Yes | No |
| S3 Integration | No | No | Yes (native) |
| Best performance | Good general-purpose | Good Windows IOPS | Extreme parallel HPC |
| Cost per GB | Lower | Medium | Higher (worth it for HPC) |

---

## Key Points / Exam Tips

- **FSx for Windows** = SMB + AD + NTFS + DFS. The only FSx that natively integrates with Active Directory.
- **FSx for Lustre** = HPC + S3-integrated + maximum parallel throughput. Scratch = temporary; Persistent = durable.
- **FSx for NetApp ONTAP** = NFS + SMB + iSCSI all at once; ONTAP feature parity.
- **FSx for OpenZFS** = ZFS feature parity on AWS over NFS.
- **FSx File Gateway** (Storage Gateway type) gives on-premises low-latency SMB access to FSx for Windows during hybrid migrations — pair with DataSync to move the actual data.
- Cost effectiveness: even though FSx for Lustre costs more per GB, it may reduce total cost by cutting compute hours on HPC jobs.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "SMB / Active Directory / Windows file shares" | FSx for Windows File Server |
| "HPC / ML training / tied to S3 / extreme parallel throughput" | FSx for Lustre |
| "DFS namespace for Windows workloads" | FSx for Windows File Server |
| "On-prem NetApp → AWS, minimal changes" | FSx for NetApp ONTAP |
| "On-prem ZFS → AWS" | FSx for OpenZFS |
| "Temporary HPC scratch storage" | FSx for Lustre — Scratch deployment type |
| "NFS + SMB + iSCSI all at once" | FSx for NetApp ONTAP |
