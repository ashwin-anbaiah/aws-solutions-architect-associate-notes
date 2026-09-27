# Disaster Recovery Strategies

## Core Concepts

**Disaster Recovery (DR)** is the process of preparing for and recovering from a disaster — natural, technical, or human-caused.

### Two Key Metrics

| Metric | Definition | What It Measures |
|---|---|---|
| **RPO** (Recovery Point Objective) | Maximum acceptable time since the last data recovery point | How much data you can afford to lose |
| **RTO** (Recovery Time Objective) | Maximum acceptable time between service interruption and restoration | How long the app can be down |

- **Low RPO** = very frequent backups / continuous replication → more expensive.
- **Low RTO** = infrastructure already running or quickly started → more expensive.
- The **faster** the recovery, the **more it costs** (pre-provisioned infrastructure).

---

## The 4 DR Strategies (Slowest → Fastest)

### 1. Backup & Restore

```
On-premises → Backup → S3/Glacier
On disaster → Provision infrastructure from scratch → Restore data
```

- **RPO:** Hours to days (depends on backup frequency)
- **RTO:** Hours to days (time to provision + restore)
- **Cost:** Lowest — pay only for backup storage; no pre-running infrastructure
- **Before disaster:** Regularly back up data to S3/Glacier; store EBS/RDS snapshots; create AMIs
- **After disaster:** Launch EC2 instances from AMIs; restore databases from snapshots
- **Use when:** Non-critical workloads where extended downtime is acceptable; cost is the primary constraint

---

### 2. Pilot Light

```
On-premises (primary) → Replicate data continuously → AWS (minimal infrastructure, core services only)
On disaster → Scale up the pre-deployed core infrastructure
```

- **RPO:** Minutes (continuous replication)
- **RTO:** Hours (need to scale up compute, configure LBs, etc.)
- **Cost:** Low — only the "light" (core DB, etc.) is always on; compute is not pre-provisioned
- **Before disaster:** Replicate data to AWS (e.g., RDS with continuous sync); keep infrastructure templates ready
- **After disaster:** Launch EC2 instances from AMIs; scale up from the pre-existing database
- **Use when:** Critical data must be safe (low RPO) but full infrastructure doesn't need to be pre-running

---

### 3. Warm Standby

```
On-premises (primary) → Replicate → AWS (scaled-down but RUNNING version of the full stack)
On disaster → Scale up existing AWS infrastructure to full production capacity
```

- **RPO:** Minutes (continuous replication)
- **RTO:** Minutes (scale up already-running infrastructure)
- **Cost:** Medium — infrastructure is running at reduced capacity 24/7
- **Before disaster:** A smaller but fully configured environment runs continuously (smaller EC2 fleet, RDS replica)
- **After disaster:** Scale up existing running infrastructure to full production size
- **Use when:** Business-critical apps needing fast recovery (minutes RTO) without full active-active cost

> **Key difference from Pilot Light:** Warm Standby has the full stack already running (at reduced capacity); Pilot Light only has the data layer running.

---

### 4. Multi-Site Active-Active

```
Region 1 (active, full traffic) ↔ Region 2 (active, full traffic)
Data replicated in real-time (Aurora Global Database, DynamoDB Global Tables)
On disaster → Route 53 redirects traffic to healthy region
```

- **RPO:** Near-zero (real-time replication)
- **RTO:** Seconds (Route 53 health check + DNS failover)
- **Cost:** Highest — full infrastructure running in multiple regions simultaneously
- **Before disaster:** Identical production environments in 2+ Regions; Route 53 with failover routing; real-time data replication
- **After disaster:** Route 53 automatically routes all traffic to the healthy Region
- **Use when:** Mission-critical applications where even seconds of downtime are unacceptable

---

## Side-by-Side Comparison

| Strategy | RPO | RTO | Cost | Infrastructure Pre-Provisioned |
|---|---|---|---|---|
| **Backup & Restore** | Hours–Days | Hours–Days | Lowest | None (provision on disaster) |
| **Pilot Light** | Minutes | Hours | Low | Data layer only (no compute) |
| **Warm Standby** | Minutes | Minutes | Medium | Full stack at reduced capacity |
| **Multi-Site Active-Active** | Seconds–Near-zero | Seconds | Highest | Full stack at full capacity (×2) |

---

## Important Notes on RDS Multi-AZ

- RDS Multi-AZ = **High Availability** (HA), NOT Disaster Recovery.
- If someone accidentally drops a table, the Multi-AZ standby will also have the table dropped (synchronous replication).
- For DR protection against data corruption/deletion, you need **point-in-time recovery** or **cross-region snapshots** in addition to Multi-AZ.

---

## Warm Standby Architecture Pattern

- **Route 53 failover routing** detects the on-prem primary is down.
- **Pre-running EC2 instances** behind an ALB in an Auto Scaling group (already running, not launched on demand).
- **AWS Storage Gateway (stored volumes)** continuously sync on-prem data to S3.
- **Critical exam point:** any solution that uses CloudFormation to provision infrastructure AT FAILOVER TIME introduces provisioning delay — violating "least downtime." Pre-running infrastructure is essential for Warm Standby and Active-Active RTO guarantees.

---

## Key Points / Exam Tips

- Spectrum: Backup & Restore (slowest/cheapest) → Pilot Light → Warm Standby → Active-Active (fastest/most expensive).
- **Pilot Light vs Warm Standby:** Pilot Light = data layer only; Warm Standby = full stack at reduced scale.
- **Multi-AZ ≠ DR** — it protects against hardware failure, NOT against data corruption or Region outage.
- **Aurora Global Database** and **DynamoDB Global Tables** are the standard data replication tools for Active-Active DR across Regions.
- Cost is always the tradeoff — lower RTO/RPO always means higher ongoing infrastructure cost.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Lowest cost DR, extended downtime acceptable" | Backup & Restore |
| "Data must be safe, compute can be spun up after disaster" | Pilot Light |
| "Minutes RTO, full stack pre-running at reduced capacity" | Warm Standby |
| "Near-zero RTO/RPO, active in multiple regions" | Multi-Site Active-Active |
| "Route 53 + pre-running EC2/ALB + Storage Gateway, least downtime" | Warm Standby pattern |
| "Real-time replication across regions for DR" | Aurora Global Database / DynamoDB Global Tables |
