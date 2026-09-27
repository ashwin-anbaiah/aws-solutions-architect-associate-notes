# EC2 Pricing Options (On-Demand, Spot, Reserved, Savings Plans)

## Overview

EC2 offers several pricing models to match your workload's cost profile. The key variables are: **how predictable is the workload** and **how much flexibility do you have**.

---

## 1. On-Demand

**Pay for what you use, no commitment.**

- Billed **by the second** (Linux/Windows/Ubuntu/RHEL) or by the hour (some other OS types)
- No upfront cost, no term commitment
- Highest per-unit cost of all options
- Start, stop, or terminate at any time

**Best for**:
- Development, test, staging environments with unpredictable runtime
- Short-term experiments, POCs, benchmarking
- Workloads where usage cannot be predicted

**Anti-patterns**:
- Long-running steady-state workloads (use Reserved/Savings Plan instead)
- Fault-tolerant batch jobs (use Spot instead, 90% cheaper)

---

## 2. Spot Instances

**Up to 90% discount — but AWS can reclaim with 2-minute warning.**

- AWS provides spare/unused capacity at steep discounts
- Instance can be **interrupted** when AWS needs the capacity back — you get a **2-minute notification** before termination
- Can be combined with Auto Scaling groups and Spot Fleets for managed fault tolerance

**Best for**:
- Big data processing (Hadoop, Spark)
- CI/CD pipelines, batch jobs
- ML training (can checkpoint and resume)
- Video rendering, image processing

**Anti-patterns**:
- Stateful/critical workloads that cannot handle interruption (use On-Demand or Reserved instead)
- Databases holding in-memory state

**Spot strategies**:
- Be flexible with instance types (try multiple families)
- Be flexible with AZs (different AZs have different spot prices)
- Mix On-Demand for the critical baseline + Spot for the elastic overhead

---

## 3. Reserved Instances (RI)

**Commit to 1 or 3 years for up to 72% discount.**

- Reserve a specific **instance type** in a specific **Region and AZ**
- Pay whether you use the capacity or not
- Two types:
  - **Standard RI**: fixed instance type, size, and Region (maximum discount)
  - **Convertible RI**: can change instance type, OS, or tenancy during the term (slightly lower discount but more flexible)
- Payment options: Full Upfront (max discount), Partial Upfront, No Upfront (minimum discount)
- Can be bought and sold on the **AWS Reserved Instance Marketplace** if circumstances change

**Best for**:
- Steady-state, predictable, long-term workloads (databases, production servers)
- Applications where you know you'll need capacity for 1–3 years

---

## 4. Savings Plans

**Commit to a spending rate ($/hour) for 1 or 3 years.**

More flexible than RIs — discount is applied based on compute spend commitment, not specific instance attributes.

| Plan Type | Covers | Discount |
|---|---|---|
| **EC2 Instance Savings Plan** | EC2 only, specific instance family and Region | Up to 72% |
| **Compute Savings Plan** | EC2 + Fargate + Lambda, any Region | Up to 66% |
| **SageMaker Savings Plan** | SageMaker training/inference only | Separate plan needed |

**Compute Savings Plan is the most flexible**: doesn't lock you to a specific instance family or Region. If you migrate from m5 to m7g or move from us-east-1 to eu-west-1, the discount still applies.

**Best for**:
- Similar to Reserved Instances, but when you want flexibility to change instance types or Regions

---

## 5. Dedicated Instances vs Dedicated Hosts

Both provide **single-tenant hardware** (your instances don't share physical hardware with other customers):

| | Dedicated Instances | Dedicated Hosts |
|---|---|---|
| Hardware isolation | Yes — single AWS account | Yes — single AWS account |
| Visibility into physical host | No | Yes (specific host, socket/core visibility) |
| Pricing | Per-instance + $2/region/hour fee | Per-host billing |
| BYOL (Bring Your Own License) | Cannot use | Can use (per-socket/core licensing) |
| Host placement control | AWS decides | You choose which host |
| Use case | Compliance requiring single-tenant hardware | Legacy software licenses tied to physical cores/sockets |

**Key distinction**: Dedicated Instances = tenant isolation only. Dedicated Hosts = isolation + full physical host control + BYOL.

---

## 6. On-Demand Capacity Reservation (ODCR)

- **Reserve EC2 capacity in a specific AZ** without a 1 or 3-year commitment
- Pay On-Demand rate regardless of whether you use the capacity
- Create and cancel anytime
- Use case: business-critical events where you must guarantee capacity (Black Friday, major launches)
- RI and Savings Plan discounts apply to matching ODCR capacity

---

## Pricing Comparison Summary

| Model | Discount | Commitment | Interruption Risk | Best For |
|---|---|---|---|---|
| **On-Demand** | None | None | None | Unpredictable, short-term |
| **Spot** | Up to 90% | None | Yes (2-min warning) | Fault-tolerant, flexible workloads |
| **Reserved (Standard)** | Up to 72% | 1 or 3 years | None | Steady-state, known long-term |
| **Savings Plan (Compute)** | Up to 66% | 1 or 3 years | None | Steady-state with flexibility |
| **Dedicated Host** | Premium | On-demand/Reserved | None | BYOL, compliance |

**Maximum discount**: EC2 Savings Plan or Standard Reserved Instance, 3-year term, Full Upfront payment.

---

## Key Points / Exam Tips

- **Spot = 90% discount but interruptible** — only for fault-tolerant, stateless workloads
- **Dedicated Hosts (not Instances)** = use for BYOL per-socket/per-core software licenses
- **Compute Savings Plan** = covers EC2 + Fargate + Lambda (does NOT cover SageMaker — that needs its own plan)
- **Convertible RI** = can change instance type but has less discount than Standard RI
- **ODCR** = capacity guarantee with no term commitment, useful for predictable event spikes

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Fault-tolerant, can handle interruption" | Spot Instances |
| "Steady-state, 1–3 year commitment" | Reserved Instances or Savings Plan |
| "Software license per socket/core, BYOL" | Dedicated Hosts |
| "Single-tenant hardware, compliance, no license concern" | Dedicated Instances |
| "Flexible across instance families, still discounted" | Compute Savings Plan |
| "Guarantee capacity for a specific event" | On-Demand Capacity Reservation |
| "Mix of EC2 + Fargate + Lambda, fewest commitment plans" | Compute Savings Plan |
