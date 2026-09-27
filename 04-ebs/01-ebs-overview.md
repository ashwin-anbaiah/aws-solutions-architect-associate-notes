# EBS Overview

## What Is EBS?

**Amazon Elastic Block Store (EBS)** is a persistent block storage service designed to be attached to EC2 instances. Think of EBS volumes as virtual hard drives — they store your OS, applications, databases, and data files, and they persist independently of the EC2 instance lifecycle.

EBS is a **Storage Attached Network (SAN)** device — the storage is external to the physical host, attached over the network. This means you can detach a volume from one instance and attach it to another (within the same AZ).

---

## EBS Key Characteristics

| Property | Details |
|---|---|
| **Attachment** | Network-attached to an EC2 instance |
| **Persistence** | Data persists beyond instance stop/terminate (unless configured to delete on termination) |
| **Scope** | Tied to a specific **Availability Zone** — cannot attach an EBS volume to an instance in a different AZ |
| **Size range** | 1 GiB to 64 TiB (depending on volume type) |
| **Durability** | 99.999% within an AZ (automatically replicated within the AZ) |
| **Availability** | 99.9% |
| **Encryption** | Supported via AWS KMS |
| **Backups** | Via EBS Snapshots (stored in S3) |

---

## EBS Key Terminology

**IOPS** (Input/Output Operations Per Second): How many read/write operations per second the volume can handle. Important for transaction-heavy workloads like databases.

**Throughput** (MB/s): How much data can be transferred per second. Important for workloads reading/writing large sequential files (big data, log processing).

**Baseline IOPS**: The default guaranteed performance for your volume type before bursting.

**Provisioned IOPS (PIOPS)**: You explicitly set the IOPS you want (up to 256,000 IOPS with io2 Block Express). You pay for exactly what you provision.

**Burst Performance / Burst Credits**: For gp2 volumes — accumulate credits when idle, spend them to temporarily exceed baseline IOPS during spikes.

---

## EBS Root Volume vs Data Volumes

Every EC2 instance has a **root volume** containing the operating system. You can also attach additional **data volumes**.

| Volume | Purpose | Default Delete Behavior |
|---|---|---|
| **Root volume** | OS + system files | Deleted when instance terminates (`DeleteOnTermination = true` by default) |
| **Data volumes** | Application data, databases | NOT deleted when instance terminates (`DeleteOnTermination = false` by default) |

You can change the `DeleteOnTermination` attribute — useful if you want to preserve the root volume after a termination (for forensics, recovery, etc.).

---

## EBS vs Instance Store

EC2 instances can have two types of storage:

| | EBS | Instance Store |
|---|---|---|
| Location | External (network-attached) | Physical disk on the host machine |
| Persistence | Persistent — survives stop/start/terminate | **Ephemeral** — data lost on stop, terminate, or host failure (survives reboot) |
| Performance | Very good (some network overhead) | Highest raw I/O (no network hop) |
| Cost | Billed separately per GB + IOPS | **Free** (included in instance pricing) |
| Resizable/detachable | Yes | No |
| Best for | OS, databases, anything needing persistence | Temp files, cache, scratch space, buffers |

**When to use Instance Store**: the application handles its own replication (e.g., distributed systems like Cassandra where each node replicates data to others), and you need maximum IOPS at minimum cost. The ephemeral nature is acceptable because the app handles instance loss.

---

## EBS Volume AZ Constraint

EBS volumes are **AZ-scoped**. To move a volume to a different AZ:
1. Create a **snapshot** of the volume
2. Create a **new volume** from the snapshot in the target AZ

```
AZ1: EC2 with EBS Volume
         ↓ Create Snapshot
Snapshot (stored in S3, Region-wide)
         ↓ Create New Volume in AZ2
AZ2: New EBS Volume (identical data)
```

---

## EBS-Optimized Instances

EBS-Optimized instances have a **dedicated network path for EBS I/O**, separate from other instance network traffic. This eliminates contention between EBS I/O and regular network traffic, giving you the full provisioned performance of your EBS volumes.

Most modern instance types have EBS optimization enabled by default.

---

## Key Points / Exam Tips

- **EBS is AZ-scoped** — cannot cross AZ boundaries directly; use snapshots to move data
- **Root volume deleted by default** on termination; data volumes are NOT deleted by default
- **Instance Store = ephemeral, fastest, free** — lost on stop/terminate
- **EBS = persistent, slower than instance store** — costs extra but survives lifecycle events
- **EBS is replicated within the AZ** — high durability within a single AZ, NOT multi-AZ

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Temporary/scratch storage, max IOPS, cheapest" | Instance Store |
| "Persistent storage that survives instance stop" | EBS |
| "Move EBS volume to another AZ" | Snapshot → create new volume in target AZ |
| "Lost on instance termination" | Instance Store (and default root EBS) |
| "High IOPS, ephemeral, app replicates data" | Instance Store |
