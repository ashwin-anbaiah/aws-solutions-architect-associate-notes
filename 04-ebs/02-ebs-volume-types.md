# EBS Volume Types (gp3, io2, st1, sc1)

## Overview

EBS offers several volume types optimized for different workload characteristics. The main split is **SSD** (good for random I/O, IOPS-sensitive workloads) vs **HDD** (good for sequential throughput, streaming workloads).

---

## Volume Types Quick Reference

| Type | Category | Max IOPS | Max Throughput | Max Size | Boot Volume? | Key Use Case |
|---|---|---|---|---|---|---|
| **gp3** | General Purpose SSD | 16,000 | 1,000 MB/s | 16 TiB | Yes | Broad workloads, OS, databases |
| **gp2** | General Purpose SSD | 16,000 | 250 MB/s | 16 TiB | Yes | Legacy general purpose |
| **io2 Block Express** | Provisioned IOPS SSD | 256,000 | 4,000 MB/s | 64 TiB | Yes | High-performance, mission-critical DBs |
| **io1** | Provisioned IOPS SSD | 64,000 | 1,000 MB/s | 16 TiB | Yes | I/O-intensive databases |
| **st1** | Throughput Optimized HDD | 500 | 500 MB/s | 16 TiB | **No** | Big data, sequential throughput |
| **sc1** | Cold HDD | 250 | 250 MB/s | 16 TiB | **No** | Infrequently accessed, lowest cost |

---

## General Purpose SSD (gp2 and gp3)

### gp3 (Current Generation — Preferred)

- **Baseline: 3,000 IOPS and 125 MB/s throughput** — included at no extra cost regardless of volume size
- Can **independently** provision up to 16,000 IOPS and 1,000 MB/s throughput (for an extra charge)
- **More cost-effective than gp2** — gp3 is generally 20% cheaper for equivalent performance
- Use for: OS volumes, web servers, development environments, moderate-traffic databases

### gp2 (Previous Generation)

- IOPS scales with volume size: **3 IOPS per GB** (a 100 GB volume = 300 IOPS baseline)
- **Burst model**: accumulate burst credits when below baseline; spend credits to burst up to 3,000 IOPS
- Max 16,000 IOPS at 5.33+ TiB (baseline hits maximum)
- No independent throughput/IOPS configuration — IOPS always tied to volume size

**gp2 → gp3 migration**: For existing gp2 volumes, migrating to gp3 is generally cost-effective since you get the same IOPS at lower cost.

---

## Provisioned IOPS SSD (io1 and io2 Block Express)

### io2 Block Express (Current Generation)

- Up to **256,000 IOPS** and **4,000 MB/s** throughput
- Maximum size: **64 TiB**
- **Sub-millisecond latency**
- **99.999% durability** (vs 99.9% for gp3/io1)
- Use for: Oracle RAC, SAP HANA, mission-critical databases requiring both extreme IOPS and high durability

### io1 (Previous Generation)

- Up to **64,000 IOPS** per volume (on supported instance types)
- **Max IOPS:GB ratio = 50:1** (you can provision up to 50 IOPS per GB of volume size)
- Use for: high-performance databases like MySQL, PostgreSQL, MongoDB, Cassandra

**When to use Provisioned IOPS**: When you need **consistent, guaranteed** IOPS — not burstable. Unlike gp2/gp3 which can burst but may be throttled, io1/io2 delivers what you provisioned, always.

**Multi-Attach**: Only io1 and io2 volumes support Multi-Attach (see the Multi-Attach file for details).

---

## Throughput Optimized HDD (st1)

- **Cannot be used as a boot volume** (HDD types cannot boot)
- Optimized for **large sequential reads and writes** — not random I/O
- Up to 500 IOPS and 500 MB/s throughput
- Use for: Kafka logs, Hadoop data nodes, data warehouse staging, log processing
- Cost-effective for streaming/sequential big data workloads

---

## Cold HDD (sc1)

- **Cannot be used as a boot volume**
- **Lowest cost** EBS volume type
- Designed for **infrequently accessed** data
- Up to 250 IOPS and 250 MB/s throughput
- Use for: file archives, cold data storage, infrequently accessed large datasets

---

## Comparison: When to Use What

| If you need... | Use |
|---|---|
| OS root volume | gp3 (or gp2 for compatibility) |
| Web server, dev/test, general workloads | gp3 |
| Relational DB with predictable, moderate IOPS | gp3 (provision IOPS as needed) |
| I/O-intensive database with guaranteed high IOPS | io1 or io2 |
| Extreme performance, mission-critical (>64K IOPS) | io2 Block Express |
| Streaming big data, Kafka, sequential throughput | st1 |
| Cold, infrequently accessed archival data | sc1 |

---

## gp2 Burst Model (Common Exam Topic)

- Initial credit balance: 5.4 million IOPS credits (= 30 minutes of 3,000 IOPS burst)
- Credit earn rate: 3 IOPS/GB/second when the volume is below baseline
- Credit spend rate: when IOPS exceeds baseline
- Once credits run out, IOPS drops to baseline (3 × size in GB)

**Implication**: Small gp2 volumes with low baselines (e.g., 100 GB = 300 IOPS baseline) that frequently burst will eventually run out of credits and be throttled. For consistently high IOPS, either size up the gp2 volume or switch to gp3/io1 where IOPS is provisioned independently.

---

## Key Points / Exam Tips

- **st1 and sc1 CANNOT be boot volumes** — SSD types (gp2, gp3, io1, io2) can be boot volumes
- **io1/io2 = only types supporting Multi-Attach** — and only within the same AZ
- **gp3 = current best general-purpose choice** — lower cost, independent IOPS/throughput tuning
- **io2 Block Express = highest performance** — 256K IOPS, 64 TiB, 5-nines durability
- **Large IOPS number in question (e.g., 20,000+)** → io1 or io2 (gp3 max is 16,000)

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Guaranteed/consistent IOPS, no burst model" | io1 or io2 |
| "Multi-Attach across multiple EC2 instances" | io1 or io2 only |
| "Big data sequential throughput, not random I/O" | st1 |
| "Infrequently accessed, lowest cost" | sc1 |
| "Over-provisioned io1, want to reduce cost with occasional bursts" | Migrate to gp3 |
| "Need >16,000 IOPS on a single volume" | io1 or io2 |
