# EC2 Placement Groups

## What Is a Placement Group?

A **Placement Group** is a logical grouping of EC2 instances that controls how AWS physically places them on the underlying infrastructure. By default, AWS distributes instances across hardware to avoid failures — but sometimes you want more control, either for performance (pack instances together) or for fault isolation (spread them far apart).

Placement groups let you tell AWS exactly how to place your instances.

---

## The Three Placement Group Types

### 1. Cluster Placement Group

**Goal: Maximum network performance between instances.**

AWS packs all instances as close together as possible — typically on the same physical rack, within the same Availability Zone.

```
Cluster Placement Group (single AZ)
┌──────────── Same rack/network segment ─────────────┐
│  EC2-1 ←──── 10 Gbps/flow ────▶ EC2-2              │
│  EC2-3 ←────────────────────────▶ EC2-4             │
└─────────────────────────────────────────────────────┘
```

| Property | Details |
|---|---|
| **AZ span** | Single AZ only |
| **Network performance** | Up to 10 Gbps per flow, low latency |
| **Risk** | High correlated failure risk — a rack failure could take down all instances |
| **Use case** | HPC (High Performance Computing), tightly coupled node-to-node communication, ML training jobs, big data jobs that need fast in-cluster data exchange |

**The tradeoff**: you get maximum speed but minimum fault tolerance — all your eggs in one basket (rack).

---

### 2. Spread Placement Group

**Goal: Maximum fault isolation.**

AWS places each instance on **distinct underlying hardware** — different physical racks with separate power and networking. This minimizes correlated hardware failures.

```
Spread Placement Group
AZ1: EC2-1 on Rack A    AZ2: EC2-3 on Rack C
AZ1: EC2-2 on Rack B    AZ3: EC2-4 on Rack D
(each instance on a completely different rack)
```

| Property | Details |
|---|---|
| **AZ span** | Can span multiple AZs |
| **Max instances** | **7 instances per AZ per group** (hard limit) |
| **Network performance** | Standard (no enhancement — instances are spread out) |
| **Risk** | Minimum correlated failure risk — rack failure only affects one instance |
| **Use case** | A small number of critical instances (e.g., primary DB, primary and replica, ZooKeeper nodes) where you need to guarantee they don't fail together |

**The tradeoff**: maximum fault isolation, but a hard limit of 7 per AZ and no network performance benefit.

---

### 3. Partition Placement Group

**Goal: Fault isolation for large distributed systems.**

AWS divides instances into logical **partitions**, and each partition does not share physical hardware with other partitions. Within a partition, multiple instances can run.

```
Partition Placement Group
AZ1 Partition 1 (own hardware)  ─  EC2-1, EC2-2, EC2-3
AZ1 Partition 2 (own hardware)  ─  EC2-4, EC2-5, EC2-6
AZ1 Partition 3 (own hardware)  ─  EC2-7, EC2-8, EC2-9
```

| Property | Details |
|---|---|
| **AZ span** | Can span multiple AZs |
| **Max partitions** | Up to **7 partitions per AZ** |
| **Max instances** | Hundreds (no per-instance limit, only per-partition hardware limit) |
| **Visibility** | You can see which partition each instance is in |
| **Use case** | Large distributed/replicated systems like Hadoop, Cassandra, Kafka, HDFS |

**Why this matters for distributed systems**: Cassandra replicates data across nodes. If you configure Cassandra to place its replicas across partitions, a hardware failure in one partition only affects the instances there — other partitions (with their own replicas) are unaffected. The application remains available.

---

## Comparison Table

| | Cluster | Spread | Partition |
|---|---|---|---|
| Goal | Performance | Fault isolation (small scale) | Fault isolation (large scale) |
| AZ scope | Single AZ | Multiple AZs | Multiple AZs |
| Instance limit | No limit | 7 per AZ | 7 partitions per AZ, unlimited instances |
| Network performance | Enhanced (up to 10 Gbps) | Standard | Standard |
| Correlated failure risk | High (same rack) | Low (separate racks) | Medium (per partition) |
| Best for | HPC, ML training, tightly coupled | Critical isolated instances | Hadoop, Kafka, Cassandra |

---

## EFA and Cluster Placement Groups

For HPC workloads, you often pair a **Cluster Placement Group** with an **EFA (Elastic Fabric Adapter)**:
- **ENA (Elastic Network Adapter)**: enhanced networking, higher bandwidth, lower latency — general purpose
- **EFA (Elastic Fabric Adapter)**: ENA's capabilities PLUS **OS-bypass** — the application communicates directly with the hardware without going through the OS network stack

EFA is purpose-built for HPC and ML workloads that need extreme inter-instance communication performance (like tightly coupled MPI simulations).

---

## Practical Decision Guide

Ask yourself:

1. "Do my instances need to communicate with each other at maximum speed?" → **Cluster**
2. "Do I have a handful of critical instances that cannot all fail together?" → **Spread**
3. "Do I have a large distributed system where I need replica-level fault isolation?" → **Partition**

---

## Key Points / Exam Tips

- **Cluster** = performance, single AZ, low-latency inter-instance, high correlated failure risk
- **Spread** = fault isolation, max 7 per AZ per group, separate racks, can span AZs
- **Partition** = large distributed systems, partition-level fault isolation, up to 7 partitions per AZ
- **EFA = ENA + OS-bypass** → for HPC/ML inter-instance communication in a Cluster PG

## Trigger Words

| Exam phrase | Think |
|---|---|
| "HPC, tightly coupled, low-latency between instances" | Cluster Placement Group |
| "Minimize correlated hardware failure" | Spread Placement Group |
| "Distributed system (Hadoop, Kafka, Cassandra) with replica isolation" | Partition Placement Group |
| "Max instances per AZ in Spread group" | 7 — hard limit |
| "EFA, OS-bypass, extreme inter-instance networking" | EFA (Elastic Fabric Adapter) in Cluster PG |
| "HPC inter-node, beyond ENA" | EFA |
