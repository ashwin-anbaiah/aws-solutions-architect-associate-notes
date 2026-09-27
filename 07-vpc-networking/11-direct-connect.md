# AWS Direct Connect (DX)

## What is Direct Connect?

**AWS Direct Connect** provides a **dedicated, private physical network connection** between your on-premises data center and AWS. Traffic does NOT traverse the public internet — it travels over a private fiber connection from your facility to an AWS Direct Connect Location (colocation facility).

---

## Key Properties

| Property | Detail |
|---|---|
| **Connection type** | Dedicated physical fiber link |
| **Bandwidth** | 50 Mbps to 100 Gbps (dedicated) or 50 Mbps to 10 Gbps (hosted) |
| **Setup time** | Weeks to months (physical provisioning required) |
| **Encryption** | NOT encrypted by default — use VPN over DX or MACsec for encryption |
| **Routing** | BGP for dynamic routing |
| **Cost** | Port-hour charges + outbound data transfer (cheaper than internet rates) |

---

## Connection Types

### Dedicated Connections
- Physical Ethernet port dedicated to a single customer
- Available: **1 Gbps, 10 Gbps, 100 Gbps**
- Requested directly with AWS or through a DX Partner

### Hosted Connections
- Bandwidth from **50 Mbps to 10 Gbps**
- Provided via an **AWS Direct Connect Partner** (not directly from AWS)
- AWS applies traffic policing — excess traffic is dropped (not queued)
- More flexible capacity options, faster provisioning than dedicated

---

## Virtual Interfaces (VIFs)

| VIF Type | Connects To | Use For |
|---|---|---|
| **Private VIF** | VPC (via VGW or DX Gateway) | Access private VPC resources (EC2, RDS) |
| **Public VIF** | AWS public services | Access S3, DynamoDB, SQS, etc. over private link |
| **Transit VIF** | Direct Connect Gateway → Transit Gateway | Connect DX to multiple VPCs via TGW |

**Memory aid:** Private → VPC (private resources). Public → AWS services (public endpoints over DX). Transit → Transit Gateway.

---

## Direct Connect Gateway

- A **global resource** — accessible in all AWS regions
- Connects a single DX connection to **multiple VPCs across multiple regions**
- A DX connection attaches to the DX Gateway via a Private VIF or Transit VIF
- DX Gateway connects to VPCs via Virtual Private Gateways (VGWs) or Transit Gateway

**Benefit:** One DX connection can reach VPCs in multiple regions — without needing a separate DX connection per region.

```
On-premises → DX → DX Gateway → VPC in us-east-1
                               → VPC in us-west-2
                               → VPC in ap-southeast-1
```

### DX Gateway with Transit VIF (Scalable Architecture)
```
On-premises → DX → DX Gateway → Transit Gateway → VPC-1, VPC-2, VPC-3
```

---

## Direct Connect HA Architectures

### Resiliency Best Practices
- **Single DX connection** = no HA (physical link is a SPOF)
- **Two DX connections at the same location** = HA against device/port failure
- **Two DX connections at different locations** = HA against location failure (recommended for critical workloads)
- **DX primary + VPN failover** = cost-effective HA with internet VPN as backup

---

## Direct Connect vs. Site-to-Site VPN

| Feature | Direct Connect | Site-to-Site VPN |
|---|---|---|
| Path | Private physical link | Public internet (encrypted) |
| Setup time | Weeks to months | Minutes |
| Bandwidth | Up to 100 Gbps | ~1.25 Gbps per tunnel |
| Latency | Consistent, low | Variable |
| Encryption | Not by default | Yes (IPSec) |
| Cost | Higher (port charges) | Lower |

---

## Key Points / Exam Tips

- Direct Connect is **not encrypted** — to encrypt DX traffic, add a **VPN tunnel on top of DX** or use **MACsec** (Layer 2 encryption where supported)
- **Public VIF** is for accessing AWS public services (S3, SQS) — NOT for reaching private VPC resources
- **Private VIF** is for reaching private VPC resources — NOT for public AWS services
- To reach S3 over Direct Connect: use **Public VIF** to the public S3 endpoint
- DX Gateway enables one DX connection to reach **multiple VPCs in multiple regions**
- DX Gateway with Private VIF does NOT allow VPC-to-VPC routing (only on-prem to VPCs)
- For VPC-to-VPC routing over DX: use **Transit VIF → Transit Gateway**

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Consistent, low-latency, high-bandwidth to AWS" | Direct Connect |
| "Physical dedicated line to AWS" | Direct Connect |
| "Weeks to set up connectivity" | Direct Connect (not VPN) |
| "Connect DX to multiple VPCs across regions" | Direct Connect Gateway |
| "On-premises access to S3 over private path" | Public VIF on Direct Connect |
| "On-premises access to EC2/RDS over private path" | Private VIF on Direct Connect |
| "Encrypt Direct Connect traffic" | Add VPN over DX, or use MACsec |
| "TGW + Transit VIF" | Connect DX to Transit Gateway for scalable multi-VPC hybrid |
