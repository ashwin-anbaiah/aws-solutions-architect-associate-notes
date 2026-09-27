# AWS Migration Services Overview

## The Migration Portfolio

AWS provides a suite of services covering the full migration lifecycle: assess → mobilize → migrate → optimize. The key services are:

| Service | Phase | Purpose |
|---|---|---|
| **AWS Migration Hub** | Plan + Track | Central dashboard to track migration progress across all tools and Regions |
| **Application Discovery Service (ADS)** | Assess | Discover and inventory on-prem servers, apps, and dependencies |
| **Migration Evaluator** | Assess | Business case analysis: estimate cost savings and right-sizing recommendations |
| **AWS MGN (Application Migration Service)** | Migrate | Rehost (Lift & Shift) — block-level server replication to EC2 |
| **AWS DMS (Database Migration Service)** | Migrate | Replatform — migrate databases to RDS/Aurora/Redshift/DynamoDB |
| **App2Containers (A2C)** | Migrate | Containerize existing Java/.NET apps for ECS/EKS |
| **AWS Transform** | Modernize | AI-driven modernization, automated code refactoring (new Nov 2025) |

---

## AWS Migration Hub

- **Centralized tracking dashboard** for all migration activity across AWS and third-party tools.
- Aggregates discovery data from ADS and migration progress from MGN, DMS, and other tools.
- Visualizes migration status by application, by server, by region.
- Supports multi-workload migration programs: mainframe, Windows/.NET, VMware, custom workloads.
- Features (as of Nov 2025): automated code refactoring, dependency analysis, integrated testing and validation.

---

## Application Discovery Service (ADS)

Discovers and collects inventory of on-premises IT infrastructure before migration.

| Mode | How It Works | Best For |
|---|---|---|
| **Discovery Agent** | Installed on each server; collects detailed OS, process, network dependency data | Deep dependency mapping for complex environments |
| **Agentless Collector** | Deployed as a VM in vCenter; discovers VMs without touching each server | VMware environments; broad inventory, less detail |

- **Output:** server inventory, hardware specs, CPU/memory/storage usage, network dependencies.
- Data can be visualized in **Amazon QuickSight** for analysis.
- Feeds right-sizing recommendations for EC2 instances.
- Integrates with Migration Hub for centralized planning.

---

## Migration Evaluator (formerly TSO Logic)

- Analyzes on-premises server data to build a **business case for cloud migration**.
- Provides cost estimates, savings projections, and right-sizing recommendations.
- Used in the **assess phase** before any actual migration begins.

---

## Service Selection by Migration Type

| What You're Migrating | Service | Strategy |
|---|---|---|
| Application servers (any OS) | **AWS MGN** | Rehost (Lift & Shift) |
| Databases (homogeneous or heterogeneous) | **AWS DMS** (+ SCT for schema conversion) | Replatform |
| Java/.NET apps → containers | **App2Containers (A2C)** | Replatform |
| VMware workloads | **VMware Cloud on AWS** | Relocate |
| Full inventory discovery | **ADS (Discovery Agent or Agentless)** | Assess |
| Cost/savings estimation | **Migration Evaluator** | Assess |

---

## Key Points / Exam Tips

- **Migration Hub** = the control plane / tracking dashboard — it doesn't do the migration itself.
- **ADS** = pre-migration discovery; agent for detailed data, agentless for VMware broad inventory.
- **MGN** = servers to EC2 (rehost). **DMS** = databases to managed DB services (replatform).
- MGN is the modern replacement for the deprecated Server Migration Service (SMS).
- DMS supports both homogeneous (MySQL → RDS MySQL) and heterogeneous (Oracle → Aurora PostgreSQL via SCT).
- App2Containers automates the containerization of existing apps — no rewrite needed.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Track migration progress centrally across services/regions" | AWS Migration Hub |
| "Discover what's running on-prem before migrating" | Application Discovery Service |
| "Estimate cost savings and right-size for cloud" | Migration Evaluator |
| "Lift-and-shift servers to EC2" | AWS MGN (Application Migration Service) |
| "Migrate database to RDS / Aurora" | AWS DMS |
| "Containerize existing Java app for ECS/EKS" | App2Containers |
