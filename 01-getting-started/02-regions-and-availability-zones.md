# AWS Regions and Availability Zones

## AWS Regions

An **AWS Region** is a distinct geographic area that contains a cluster of Availability Zones. Each Region is a fully independent infrastructure environment — it has its own power, networking, and connectivity.

### Region Naming

Every Region has a human-readable name and a code:

| Region Name | Code |
|---|---|
| US East (N. Virginia) | `us-east-1` |
| US West (Oregon) | `us-west-2` |
| EU (London) | `eu-west-2` |
| Asia Pacific (Mumbai) | `ap-south-1` |
| Asia Pacific (Singapore) | `ap-southeast-1` |

### How to Choose a Region

The five decision factors (in rough priority order for the exam):

1. **Low latency** — deploy where your users are
2. **Data residency and compliance** — regulations like GDPR may require data to stay in a specific country
3. **Disaster recovery** — separate Regions give geographic isolation for DR
4. **Service availability** — some newer or specialized services are only in certain Regions
5. **Pricing** — costs vary by Region (sometimes 10–20% difference for the same instance type)

---

## Availability Zones (AZs)

An **Availability Zone** is one or more discrete data centers within a Region. Each AZ has:
- Redundant power (separate grid feeds, generators)
- Redundant networking (multiple fiber paths)
- Physical separation from other AZs (different buildings, different flood plains in most cases)

AZs within the same Region are connected by **low-latency private fiber links** — typically under 1ms round-trip time.

### Why at Least 3 AZs per Region?

AWS designs Regions with a minimum of 3 AZs so that:
- **High availability**: your app survives a failure in one AZ
- **Durability**: services like S3 automatically distribute data across AZs
- **Load distribution**: you can spread compute across AZs and load-balance across them

```
Mumbai Region (ap-south-1)
├── AZ1 (ap-south-1a)  — Data Center cluster A
├── AZ2 (ap-south-1b)  — Data Center cluster B
└── AZ3 (ap-south-1c)  — Data Center cluster C
```

### AZ Names vs AZ IDs — A Subtle but Important Distinction

- **AZ name** (e.g., `us-west-2a`) is randomly mapped per AWS account. Your `us-west-2a` and my `us-west-2a` may be different physical data centers.
- **AZ ID** (e.g., `usw2-az2`) is a consistent, globally-fixed identifier tied to the actual physical location — the same ID always means the same physical AZ across all accounts.

**Why this matters**: If you're sharing resources across AWS accounts (e.g., in a multi-account org), coordinate using AZ IDs, not names. Otherwise you might think you're in the same AZ when you're not — causing unexpected cross-AZ latency.

---

## Multi-AZ Architecture Pattern

The standard pattern for highly available applications on AWS:

```
Region
├── AZ1: EC2 instance + RDS Primary
├── AZ2: EC2 instance + RDS Standby (Multi-AZ)
└── AZ3: EC2 instance (optional, for more headroom)
       ↑
    All sit behind an Elastic Load Balancer
```

The load balancer distributes traffic across AZs. If AZ1 fails, the LB stops sending traffic there; RDS automatically fails over to the standby in AZ2.

---

## Key Points / Exam Tips

- **Minimum 3 AZs per Region** (some newer Regions may have 2 during initial launch)
- **Each AZ = physically separate, independent data center cluster** — separate power, network, and flood plain
- **Low-latency fiber** connects AZs within a Region — sub-millisecond
- **"High availability" on AWS = deploy across at least 2 AZs** — one AZ is a single point of failure
- **AZ name ≠ AZ ID across accounts** — use AZ IDs when coordinating multi-account architectures
- Most AWS managed services (RDS Multi-AZ, ELB, EFS) handle AZ distribution automatically

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Survive AZ failure" | Deploy across 2+ AZs |
| "High availability" | Multi-AZ deployment |
| "Same Region, different AZs" | Low-latency, private, connected |
| "Match physical AZs across accounts" | Use AZ IDs, not AZ names |
| "Durability" in the context of S3/RDS | Multi-AZ data replication |
