# Cost Optimization

## Overview

AWS cost optimization involves choosing the right pricing models, right-sizing resources, eliminating waste, and using managed intelligent-tiering features. The main levers are: **Savings Plans, Reserved Instances, Spot Instances, rightsizing, and S3 Intelligent-Tiering**.

---

## Savings Plans

**Savings Plans** offer up to **66% discount** compared to On-Demand pricing in exchange for a commitment to a consistent amount of usage (measured in $/hour) for 1 or 3 years.

| Type | Flexibility | Discount | Applies To |
|---|---|---|---|
| **Compute Savings Plan** | Highest — any instance family, region, OS, tenancy | Up to 66% | EC2, Fargate, Lambda |
| **EC2 Instance Savings Plan** | Instance family in a specific region | Up to 72% | Specific EC2 family in one region |
| **SageMaker Savings Plan** | SageMaker instance usage | Up to 64% | SageMaker ML instances |

- Savings Plans are **recommended by Cost Explorer** based on your last 7/30/60 days of on-demand usage.
- Unused commitment is NOT refunded — choose a level you will confidently use.
- Compute Savings Plans apply across EC2 instance families, sizes, regions, OS types, and even Fargate/Lambda.

---

## Reserved Instances (RIs)

**Reserved Instances** commit to a specific instance type/region/OS for 1 or 3 years in exchange for a discount (up to 75% vs On-Demand).

| RI Type | Flexibility | Discount | Commitment |
|---|---|---|---|
| **Standard RI** | Locked to instance type, region, OS, tenancy | Highest discount | 1 or 3 years |
| **Convertible RI** | Can change instance type, OS, tenancy | Lower discount | 1 or 3 years |
| **Scheduled RI** | Reserved for specific recurring time windows (deprecated) | — | Time-based |

| Payment Option | Upfront Cost | Hourly Cost | Total Savings |
|---|---|---|---|
| **All Upfront** | Highest | Zero | Maximum savings |
| **Partial Upfront** | Medium | Some | Good savings |
| **No Upfront** | Zero | Higher | Smaller savings |

### RI Sharing via Organizations
- Unused RIs from one account can be applied to matching usage in another account in the same Organization — reduces waste.

---

## Spot Instances

- **Up to 90% cheaper** than On-Demand.
- AWS can reclaim Spot instances with a **2-minute warning** when capacity is needed.
- **Best practices:**
  - Use for **fault-tolerant, stateless, interruptible workloads** (batch, CI/CD, rendering, HPC)
  - Use **Spot Fleets** — define multiple instance types/AZs so AWS can fulfill your capacity from the pool with available capacity
  - Use **capacity-optimized allocation strategy** for Spot Fleet to minimize interruptions
  - Save checkpoints and handle the 2-minute interruption notice gracefully

---

## AWS Compute Optimizer and Rightsizing

**AWS Compute Optimizer** analyzes your actual EC2/EBS/Lambda usage and recommends right-sizing:
- **Over-provisioned instances** — downsize to save money
- **Under-provisioned instances** — upsize for better performance
- Based on CloudWatch metrics (CPU, memory, network, disk utilization)

**Cost Explorer Rightsizing Recommendations:**
- Cost Explorer also surfaces EC2 rightsizing recommendations
- Compares current instance size to what you actually need based on utilization

---

## S3 Cost Optimization

| Approach | Service | How It Saves |
|---|---|---|
| **Unknown access patterns** | S3 Intelligent-Tiering | Auto-moves objects between Standard/IA/Archive tiers; no retrieval fees |
| **Known infrequent access** | S3 Standard-IA | Cheaper storage; pay for retrieval |
| **Re-creatable data, IA** | S3 One Zone-IA | ~20% cheaper than Standard-IA; single AZ |
| **Long-term archive, minutes retrieval** | S3 Glacier Instant Retrieval | Cheapest with millisecond access |
| **Long-term archive, hours OK** | S3 Glacier Deep Archive | Absolute cheapest storage |
| **Lifecycle policies** | S3 Lifecycle | Automatically transition/delete objects by age |

---

## Cost Optimization Summary Table

| Lever | Savings | Use When |
|---|---|---|
| Savings Plans (Compute) | Up to 66% | Consistent usage, want flexibility across EC2/Fargate/Lambda |
| Reserved Instances (Standard) | Up to 75% | Steady-state workloads, specific instance type committed |
| Spot Instances | Up to 90% | Fault-tolerant, interruptible batch/HPC/CI-CD |
| Rightsizing (Compute Optimizer) | 10–40% | Over-provisioned resources identified in CloudWatch |
| S3 Intelligent-Tiering | Variable | Unknown/changing access patterns for S3 |
| S3 Lifecycle Policies | Variable | Known aging patterns — move to IA/Glacier, delete old data |
| VPC Gateway Endpoints (S3/DynamoDB) | Data transfer cost savings | Route S3 traffic within VPC without NAT Gateway |

---

## Key Points / Exam Tips

- **Compute Savings Plans** = most flexible; applies to EC2 + Fargate + Lambda automatically; best when you have mixed workloads.
- **EC2 Instance Savings Plans** = more discount but locked to a specific instance family in one region.
- **RIs** (Standard) give the highest discount but least flexibility; Convertible RI allows changing the instance type/OS.
- **Spot Instances** = NOT for workloads that cannot tolerate interruption (databases, stateful apps).
- Cost Explorer **recommends Savings Plans** based on your last N days of on-demand usage — use this as a starting point.
- Compute Optimizer = right-sizing recommendations based on actual CloudWatch utilization data.
- S3 Intelligent-Tiering has a **small monitoring fee** per object but no retrieval fees — best for unpredictable patterns.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Flexible savings commitment for EC2 + Fargate + Lambda" | Compute Savings Plan |
| "Maximum discount for specific EC2 instance family in one region" | EC2 Instance Savings Plan |
| "Interrupt-tolerant batch workloads, cheapest compute" | Spot Instances |
| "Right-size over-provisioned EC2 based on actual usage" | AWS Compute Optimizer |
| "Unknown S3 access patterns, auto-tiering, no retrieval fees" | S3 Intelligent-Tiering |
| "Cheapest long-term S3 archival, hours retrieval OK" | S3 Glacier Deep Archive |
| "RI purchased by Account A applied to Account B in same org" | Consolidated billing RI sharing (Organizations) |
