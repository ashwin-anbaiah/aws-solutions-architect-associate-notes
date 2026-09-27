# Edge Locations, Local Zones, Wavelength Zones, and Outposts

These are the "extended" infrastructure components that sit outside or adjacent to the main Region + AZ model. Each solves a specific latency or compliance problem.

---

## Edge Locations

**What they are**: Points of presence (PoPs) globally distributed to put AWS infrastructure as close to end users as possible.

**Primary users**:
- **Amazon CloudFront** (CDN) — caches static and dynamic content at edge locations, reducing round-trip time for users
- **Amazon Route 53** (DNS) — resolves DNS queries from edge locations near the user

**Scale**: 600+ Edge Locations and Regional Edge Caches across 90+ cities worldwide

**Key mental model**: Think of Edge Locations as a global "fast-lane" delivery network. Your content lives in an S3 bucket in us-east-1, but CloudFront copies it to edge locations near users — so a user in Tokyo gets the file from a local cache, not from Virginia.

```
User in Tokyo → CloudFront Edge Location (Tokyo) → [cache hit: serve directly]
                                                  → [cache miss: fetch from S3 origin in us-east-1]
```

**What edge locations cannot do**: They cannot run EC2, RDS, Lambda (standard), or any compute/database service. That stays in Regions.

---

## Local Zones

**What they are**: Extensions of an AWS Region placed in specific metropolitan areas to deliver single-digit millisecond latency to users in those cities.

**Use cases**:
- Media and entertainment production (video editing, rendering)
- Real-time gaming
- Machine learning inference at the edge
- Any application where a few milliseconds of latency are critical for users in a specific city

**How they work**: A Local Zone is linked to a parent Region. You can launch EC2, EBS, VPC subnets, and ALBs in a Local Zone. It looks and feels like an AZ, but physically sits in or near the metro area rather than in a full AWS Region campus.

**Example**: `us-west-2-lax-1a` is a Local Zone in Los Angeles, extending the `us-west-2` (Oregon) Region.

---

## Wavelength Zones

**What they are**: AWS infrastructure embedded directly within 5G carrier networks (Verizon, Vodafone, SK Telecom, etc.).

**Use cases**:
- Mobile gaming with sub-10ms latency to 5G devices
- Live video streaming from mobile devices
- Connected vehicle applications
- AR/VR on mobile

**How they work**: Compute (EC2, EBS) runs inside the telecom carrier's data center. Traffic from a 5G mobile device travels through the carrier's 5G network directly to the Wavelength Zone — it never has to traverse the public internet to reach your application server. This can cut latency from ~100ms down to single-digit milliseconds.

---

## AWS Outposts

**What they are**: Fully managed AWS infrastructure (racks of servers) physically installed in your own data center or co-location facility.

**Use cases**:
- Workloads that must remain on-premises due to data sovereignty, regulatory, or latency requirements
- Applications that need AWS APIs and services but cannot move to the cloud
- Hybrid architectures where on-prem systems need low-latency access to cloud-style infrastructure

**What runs on Outposts**: EC2, EBS, ECS, EKS, RDS, S3 (limited), ElastiCache, and more — the same services as in a Region, managed by AWS.

**How it's managed**: AWS delivers and installs the racks. AWS is responsible for maintenance, patches, and hardware management. You still pay for what you use. The Outpost is connected back to the parent AWS Region via Direct Connect or the internet.

---

## Comparison Table

| Feature | Edge Locations | Local Zones | Wavelength Zones | Outposts |
|---|---|---|---|---|
| Location | Global PoPs | Metro areas | 5G carrier networks | Your data center |
| Services | CloudFront, Route 53 | EC2, EBS, VPC, ALB | EC2, EBS | Most AWS services |
| Primary benefit | CDN caching, DNS speed | City-level latency | Sub-10ms to 5G devices | On-premises + AWS APIs |
| Who manages hardware | AWS | AWS | AWS | AWS (at your site) |
| Connected to | Parent Region | Parent Region | Parent Region | Parent Region via DX |

---

## Key Points / Exam Tips

- **Edge Locations** = CloudFront and Route 53 only — cannot run compute or database workloads
- **Local Zones** = good for city-level latency for specific workloads without moving to a full Region
- **Wavelength Zones** = 5G-specific; the only way to get sub-10ms latency to mobile 5G devices
- **Outposts** = the answer whenever a question says data/workload "must remain on-premises" but the team still wants AWS services and APIs
- **Outposts vs Local Zones**: Outposts sit inside *your* facility; Local Zones sit inside AWS's facility in a nearby city

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Cache static content globally" | CloudFront + Edge Locations |
| "Low latency for users in a specific city" | Local Zones |
| "Ultra-low latency to 5G mobile devices" | Wavelength Zones |
| "Data must stay on-premises, but need AWS APIs" | AWS Outposts |
| "Extend AWS to your own data center" | AWS Outposts |
| "Hybrid, on-prem residency, modern cloud tooling" | Outposts (possibly + EKS Anywhere) |
