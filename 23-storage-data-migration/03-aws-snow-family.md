# AWS Snow Family

## What Is the Snow Family?

The **AWS Snow Family** is a set of physical, ruggedized edge computing and offline data transfer devices. They solve the problem of moving massive datasets (TBs to PBs) when internet bandwidth would take weeks or years and is not practical.

> **Context:** Moving 10 PB over a 1 Gbps link takes approximately 4 years. Snow devices ship the data physically instead.

---

## Device Comparison

| Device | Weight | Storage | Compute | Use Case |
|---|---|---|---|---|
| **Snowcone** | 4.5 lbs (2.1 kg) | 8 TB HDD or 14 TB SSD | 2 vCPU, 4 GB RAM | Small-scale edge, IoT, content distribution |
| **Snowball Edge Storage Optimized** | 49.7 lbs (22.5 kg) | Up to 80 TB HDD + 210 TB SSD | Up to 104 vCPU, 416 GB RAM | Large-scale data migration |
| **Snowball Edge Compute Optimized** | 49.7 lbs (22.5 kg) | Up to 28 TB NVMe SSD | Up to 104 vCPU, 416 GB RAM | Edge ML, full-motion video analytics, local processing |
| **Snowmobile** | Shipping container (truck) | Up to 100 PB | N/A | Exabyte-scale migrations |

> **Note:** Snowcone and Snowball Edge are currently **Service Discontinued** as of late 2024/2025 but may still appear on the SAA-C03 exam based on when exam content was last updated.

---

## AWS Snowcone

- Smallest, lightest device — portable, built for harsh environments
- Wi-Fi or Ethernet data copy
- **DataSync agent pre-installed** — supports online data transfer in addition to physical shipment
- Use cases:
  - Industrial IoT sensor/machine data collection in a factory
  - Content distribution and aggregation at remote locations
  - Edge data migration for disconnected environments

---

## AWS Snowball Edge

- Two variants: **Storage Optimized** and **Compute Optimized**
- Network: 2x 10 GbE, 1x 40 GbE, 1x 100 GbE interfaces
- Encrypted at rest and in transit; tamper-evident
- Supports EC2 AMIs for local compute workloads
- Use cases by variant:
  - **Storage Optimized:** large-scale data migrations where bandwidth is unavailable or too slow
  - **Compute Optimized:** edge computing — ML inference, video analytics, IoT data processing at remote sites

---

## AWS Snowmobile

- A literal 45-foot shipping container on a semi-truck
- Capacity: up to **100 PB per Snowmobile**
- Used for full datacenter migrations at exabyte scale
- AWS escorts the truck; GPS tracking, 24/7 surveillance

---

## OpsHub

**AWS OpsHub** is a GUI management application for Snow devices. Instead of using CLI commands to manage and monitor Snow devices, OpsHub provides a point-and-click interface for:
- Setting up and managing Snow devices
- Transferring files and monitoring transfer progress
- Running compute workloads (EC2 instances) on the device
- Available for both Windows and Mac

---

## Snow Family Workflow

1. Request a device in AWS Console
2. Device arrives at your site
3. Connect device to local network
4. Copy data to the device (NFS/S3-compatible interface)
5. Ship device back to AWS
6. AWS ingests data into your S3 bucket
7. Device is wiped (AWS sanitizes all data after transfer)

> **Important:** Snowball devices **cannot write directly to S3 Glacier**. Data lands in an S3 bucket first; use an S3 lifecycle policy (zero-day transition) to move to Glacier Deep Archive immediately.

---

## Key Points / Exam Tips

- Snow = offline, physical data transfer when online transfer is impractical due to bandwidth or time constraints.
- Snowcone = smallest/lightest; has DataSync agent built in for hybrid online/offline use.
- Snowball Edge = workhorse for TBs–PBs migrations; compute variant supports local EC2 workloads.
- Snowmobile = 100 PB per truck; exabyte-scale only.
- Data is **encrypted on the device** — AWS-managed keys.
- Snowball → S3 first; then lifecycle policy → Glacier Deep Archive for cheapest long-term archival.
- OpsHub = GUI for Snow device management (no CLI required).

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Petabytes to migrate, slow/limited bandwidth" | Snowball Edge (Storage Optimized) |
| "Exabyte-scale datacenter migration" | Snowmobile |
| "Edge IoT data collection, lightweight device" | Snowcone |
| "Edge computing at remote site (ML, video analytics)" | Snowball Edge Compute Optimized |
| "GUI to manage Snow devices" | AWS OpsHub |
| "Snowball → Glacier, cheapest long-term" | Snowball → S3 → zero-day lifecycle → Glacier Deep Archive |
