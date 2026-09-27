# EBS Multi-Attach

## What Is EBS Multi-Attach?

**EBS Multi-Attach** is a feature that allows a **single EBS volume to be attached simultaneously to multiple EC2 instances** within the same Availability Zone. Each attached instance has full read and write access to the shared volume.

This turns a traditionally single-instance block storage device into something that can be accessed concurrently — similar to how a shared NFS mount works, but at the block level.

---

## Constraints

Multi-Attach has strict requirements — all of these must be true:

| Constraint | Detail |
|---|---|
| **Volume type** | Only **io1 and io2** volumes (Provisioned IOPS SSD). gp2, gp3, st1, sc1 do NOT support Multi-Attach |
| **AZ scope** | All attached instances must be in the **same Availability Zone** as the volume |
| **Max attachments** | Up to **16 EC2 instances** simultaneously |
| **OS** | Linux only |
| **Boot volume** | **Cannot be used as a boot/root volume** — must be an additional data volume |
| **Filesystem** | Must use a **cluster-aware filesystem** (not ext4/xfs) — standard filesystems don't handle concurrent writes safely |

---

## Why You Need a Cluster-Aware Filesystem

Standard filesystems like ext4 or xfs are designed for a single writer at a time. If two instances write to the same block simultaneously using ext4, you'll get data corruption.

**Cluster-aware filesystems** (like OCFS2, or Oracle Cluster File System) have distributed locking mechanisms that coordinate concurrent access safely. Applications that manage their own I/O coordination (like Oracle RAC) can also use raw block access.

---

## Use Cases

| Use Case | Why Multi-Attach Fits |
|---|---|
| **Oracle RAC (Real Application Clusters)** | Multiple Oracle nodes share one storage volume for clustered database operations |
| **High-availability clustered applications** | Clustered apps that need shared storage with automatic failover |
| **Zero-downtime applications** | If one node fails, others continue reading/writing the shared volume without interruption |
| **Parallel analytics/rendering** | Multiple nodes process shared input/output files in parallel |

---

## EBS Multi-Attach vs EFS

Multi-Attach is often confused with EFS. Key distinction:

| | EBS Multi-Attach | Amazon EFS |
|---|---|---|
| Type | Block storage | File storage (NFS) |
| Protocol | Block (raw I/O) | NFS |
| AZ scope | Same AZ only | Multi-AZ (EFS spans AZs) |
| Max instances | 16 | Thousands |
| Filesystem management | You manage (need cluster-aware FS) | Fully managed by AWS |
| Use case | Specialized clustered apps (Oracle RAC) | General shared file access across many instances |

**For most "multiple instances sharing storage" scenarios on the exam, the answer is EFS**, not EBS Multi-Attach. Multi-Attach is for a specific niche: clustered applications (like Oracle RAC) that manage their own concurrent I/O coordination.

---

## Quick Architecture Example

```
Availability Zone (same AZ required)
├── EC2 Instance A  ──┐
├── EC2 Instance B  ──┼── io2 EBS Volume (Multi-Attach enabled)
└── EC2 Instance C  ──┘
      (all 3 can read/write simultaneously)
```

All three instances are in the same AZ. If Instance A fails, Instances B and C continue uninterrupted — the volume stays attached to them.

---

## Key Points / Exam Tips

- **Only io1 and io2** support Multi-Attach — no other EBS types
- **Same AZ only** — Multi-Attach does NOT span Availability Zones
- **Cannot be a root/boot volume** — only use as an additional data volume
- **Cluster-aware filesystem required** — standard filesystems will cause data corruption
- **Up to 16 instances** simultaneously
- **Most "shared storage across instances" scenarios = EFS**, not Multi-Attach (EFS is simpler and multi-AZ)

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Multiple EC2 instances sharing same EBS volume" | EBS Multi-Attach (io1/io2 only, same AZ) |
| "Oracle RAC shared storage" | EBS Multi-Attach |
| "Clustered application, zero downtime, shared disk" | EBS Multi-Attach |
| "Many instances share files, any AZ, Linux" | EFS (not Multi-Attach) |
| "Multi-Attach with gp3" | NOT supported — only io1/io2 |
| "Multi-Attach as boot volume" | NOT supported |
