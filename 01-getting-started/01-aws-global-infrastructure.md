# AWS Global Infrastructure

## Overview

AWS Global Infrastructure is the physical and logical backbone that lets you deploy applications worldwide with high availability, low latency, and resilience. Before picking any specific AWS service, understanding *where* things run is the foundation for every architecture decision.

---

## The Components of AWS Global Infrastructure

| Component | What It Is | Primary Use |
|---|---|---|
| **Regions** | A cluster of AZs in a geographic area | Run workloads close to users or meet data-residency requirements |
| **Availability Zones (AZs)** | Isolated data center clusters within a Region | High availability and fault isolation |
| **Edge Locations** | CDN/DNS points of presence globally | CloudFront caching, Route 53 DNS resolution |
| **Local Zones** | AWS compute placed near large cities | Ultra-low latency for specific metro areas |
| **Wavelength Zones** | AWS compute embedded in 5G carrier networks | Mobile/gaming apps needing sub-10ms latency to 5G devices |
| **Outposts** | AWS-managed hardware in your own data center | On-premises workloads that need AWS APIs and services |

---

## How It Fits Together — A Mental Model

Think of it as concentric rings:
- **Your end user** is at the outermost ring
- **Edge Locations / Wavelength Zones** sit closest to them
- **Local Zones** sit one step inward — still close but offering more services
- **Regions + AZs** are the main compute/storage/database layer
- **Outposts** bring the Region inside your facility

```
End User → Edge Location → (Local Zone) → Region (AZ1, AZ2, AZ3) ← Outpost
```

---

## Why Infrastructure Location Matters

When an architect asks "which Region should we use?", these are the five factors:

1. **Low latency for end users** — deploy close to where most users are
2. **Data residency and compliance** — some data cannot leave a country or region (e.g., GDPR in Europe)
3. **Disaster recovery** — separate Regions give you geographic isolation
4. **Pricing differences** — the same instance type can cost 10–20% more in some Regions
5. **Service availability** — not every AWS service is available in every Region

---

## Current Scale (as of 2026 — always check aws.amazon.com for the latest)

- 30+ geographic Regions
- 90+ Availability Zones
- 600+ Edge Locations and Regional Edge Caches

---

## Key Points / Exam Tips

- **Not all services are available in every Region** — always check the AWS Regional Services page when designing an architecture
- **Edge Locations are not AZs** — they only serve CloudFront and Route 53, not EC2 or RDS
- **Regions are fully independent** — a regional failure does not cascade to other Regions
- **AMIs are Region-specific** — to use an AMI in another Region, you must copy it
- Reference: **https://aws.amazon.com/about-aws/global-infrastructure/**

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Serve users globally with low latency" | CloudFront + Edge Locations |
| "High availability, survive AZ failure" | Deploy across multiple Availability Zones |
| "Disaster recovery across geographies" | Multiple Regions |
| "Regulatory / data residency requirements" | Choose specific Region, consider Outposts |
| "Extend AWS to your data center" | AWS Outposts |
| "Ultra-low latency for 5G mobile users" | Wavelength Zones |
| "Low latency for a specific metro area" | Local Zones |
