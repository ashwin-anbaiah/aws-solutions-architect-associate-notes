# EC2 Tenancy (Shared, Dedicated Instances, Dedicated Host)

## What Is EC2 Tenancy?

**Tenancy** determines how your EC2 instances are hosted on the underlying physical hardware. It answers: "do other AWS customers share the same physical machine as my instances?"

---

## Tenancy Options

### 1. Shared Tenancy (Default)

- Multiple AWS customers' virtual machines run on the **same physical host**
- AWS's hypervisor provides strong isolation — VMs cannot access each other's memory or data
- This is the standard model for almost all workloads
- Most cost-effective option

### 2. Dedicated Instances

- Your instances run on **hardware dedicated to your AWS account** — no other account's VMs run on the same physical host
- You do **not get to choose which specific host** — AWS places instances on any available host in their dedicated pool for your account
- **No visibility** into the underlying physical hardware (socket count, core count, etc.)
- **Pricing**: per-instance charge + a flat **$2/hour/region fee** (regardless of how many Dedicated Instances you run in that Region)
- Available with On-Demand, Reserved, and Spot pricing

**Use case**: Regulatory or compliance requirements that mandate single-tenant hardware — but where the exact physical host doesn't matter.

### 3. Dedicated Hosts

- Your instances run on a **specific, fully dedicated physical server** that is allocated to you
- You have **full visibility** into the host: number of sockets, number of physical cores, which instances are running on it
- You choose **which host to launch instances on** (host affinity)
- **Priced per host** (not per instance)
- **Supports Bring Your Own License (BYOL)** for software licensed per socket, per core, or per VM
- Available with On-Demand, Reserved, and Savings Plan pricing

**Use case**: Software licenses tied to specific physical hardware characteristics (Oracle Database, SQL Server Enterprise with per-core licensing, Windows Server with per-socket licensing).

---

## Dedicated Instances vs Dedicated Hosts — The Critical Distinction

| | Dedicated Instances | Dedicated Hosts |
|---|---|---|
| Hardware isolation (single-tenant) | Yes | Yes |
| Choose which specific host | No | Yes |
| Visibility into physical hardware | No | Yes (sockets, cores) |
| BYOL support | No | Yes |
| Billing unit | Per instance + $2/hr/region fee | Per host |
| Cost relative to each other | Lower | Higher |

**The exam trigger**: If the question mentions "per-socket" or "per-core" software licensing, or "BYOL" → **Dedicated Hosts** (not Dedicated Instances — Instances don't give you the host-level visibility needed for those licenses).

If the question mentions "single-tenant" or "compliance requires dedicated hardware" without mentioning BYOL licensing → **Dedicated Instances** (cheaper, sufficient for the compliance need).

---

## How VPC Tenancy Interacts with Instance Tenancy

You can set tenancy at both the VPC level and the instance level:
- **VPC tenancy = "dedicated"** → all instances launched in that VPC are Dedicated Instances by default
- **Instance tenancy = "dedicated"** → that specific instance is a Dedicated Instance
- **Rule**: If EITHER the VPC OR the instance tenancy is set to "dedicated," the instance runs as a Dedicated Instance — **dedicated always wins** over shared

---

## Real-World Analogy

- **Shared tenancy** = renting a room in a shared apartment. Safe, isolated, but you share the building.
- **Dedicated Instances** = renting the entire apartment. Just you on this floor, but the building manager decides which floor.
- **Dedicated Host** = you own the entire building. You decide which room each person goes in, and you can account for every room to comply with your lease agreement.

---

## Key Points / Exam Tips

- **Default tenancy is shared** — most workloads should use this
- **Dedicated Instances** = single-tenant hardware, no host visibility, per-instance pricing ($2/hr regional fee)
- **Dedicated Hosts** = single-tenant + host visibility + BYOL, priced per host
- **BYOL per-socket/per-core** → always Dedicated Hosts, never Dedicated Instances
- **Tenancy is OR-based**: if VPC OR instance is dedicated, the instance is dedicated

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Single-tenant hardware for compliance" | Dedicated Instances (if no BYOL/license concern) |
| "Bring Your Own License, per-socket/per-core" | Dedicated Hosts |
| "Need to know exactly which physical server" | Dedicated Hosts |
| "Dedicated but cost-matters most" | Dedicated Instances (cheaper than Hosts) |
| "Default tenancy" | Shared — multiple customers on same physical hardware |
