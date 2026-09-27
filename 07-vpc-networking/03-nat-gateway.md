# NAT Gateway

## What is a NAT Gateway?

A **NAT Gateway** (Network Address Translation Gateway) allows instances in a **private subnet** to connect outbound to the internet or other AWS services, while **preventing inbound connections** from being initiated from the internet.

**Analogy:** NAT Gateway is like a one-way valve — your private instances can make calls outward, but no one from the internet can call them directly.

---

## How It Works

1. Private subnet instance sends traffic to `0.0.0.0/0`
2. Route table directs that traffic to the NAT Gateway (in a public subnet)
3. NAT Gateway translates the private IP to its own public (Elastic) IP
4. Traffic goes out to the internet via the Internet Gateway
5. Response comes back to the NAT Gateway, which translates it back and forwards to the instance

```
Private instance (10.0.1.5)
    → NAT Gateway (public subnet, Elastic IP: 52.x.x.x)
        → Internet Gateway
            → Internet
```

---

## NAT Gateway vs. NAT Instance

| Feature | NAT Gateway (AWS Managed) | NAT Instance (EC2) |
|---|---|---|
| Management | Fully managed by AWS | You manage the EC2 |
| Security Groups | No Security Groups | Has Security Groups |
| High Availability | Automatic within one AZ | Manual setup required |
| Bandwidth | 5 Gbps, auto-scales to 100 Gbps | Limited by instance type |
| Port Forwarding | Not supported | Supported |
| Bastion Host use | No (no OS access) | Yes (can double as bastion) |
| Cost | Per hour + per GB | EC2 instance cost |
| AWS Recommendation | Preferred for new designs | Legacy |

---

## NAT Gateway Key Properties

- Must be deployed in a **public subnet**
- Must be assigned an **Elastic IP address**
- Supports protocols: **TCP, UDP, ICMP**
- **No Security Groups** — security is enforced at the instance or subnet level (NACLs)
- **Bandwidth**: starts at 5 Gbps, automatically scales to 100 Gbps
- **Cost**: charged per hour and per GB of data processed

---

## High Availability (HA) for NAT Gateway

- A NAT Gateway is **highly available within a single AZ** — it is redundant within that AZ
- To achieve **multi-AZ HA**, deploy **one NAT Gateway per AZ**
- Each private subnet in each AZ should route `0.0.0.0/0` to the NAT Gateway **in the same AZ**
- This avoids cross-AZ traffic charges and ensures resilience if one AZ fails

```
AZ-A:
  Private Subnet A → NAT-GW-A (Public Subnet A)

AZ-B:
  Private Subnet B → NAT-GW-B (Public Subnet B)
```

---

## Regional NAT Gateway (New Feature)

AWS launched a **Regional NAT Gateway** that:
- Works at VPC level, spans multiple AZs automatically
- Maintains **zonal affinity** (keeps traffic in the same AZ to save inter-AZ data transfer costs)
- Does NOT require public subnets to be present
- Has its own dedicated route table
- Comes with a dedicated Internet Gateway

---

## Private NAT Gateway

- A **Private NAT Gateway** can be used to route traffic between VPCs or to on-premises networks
- Does NOT use an Elastic IP (no public internet access)
- Used for private VPC-to-VPC connectivity with overlapping CIDRs or via Transit Gateway

---

## Key Points / Exam Tips

- NAT Gateway must be placed in a **public subnet** (standard/classic NAT GW)
- For HA: **one NAT Gateway per AZ** — single NAT GW is a single AZ dependency
- NAT Gateway does NOT work with **IPv6** — for IPv6 outbound-only access, use an **Egress-Only Internet Gateway**
- You **cannot** attach Security Groups to a NAT Gateway — use NACLs if subnet-level filtering is needed
- NAT Gateway processes traffic; if you route EC2 traffic to S3 through NAT GW you pay per-GB charges — use a **VPC Gateway Endpoint for S3** instead (free)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Private instance needs to download updates from internet" | NAT Gateway |
| "Prevent internet from initiating connections to private instances" | NAT Gateway (outbound only) |
| "NAT Gateway HA across AZs" | Deploy one NAT GW per AZ |
| "IPv6 outbound-only from private subnet" | Egress-Only Internet Gateway (not NAT GW) |
| "Port forwarding / bastion host functionality" | NAT Instance (not NAT Gateway) |
| "Reduce data transfer costs from private subnet to S3" | VPC Gateway Endpoint (instead of routing through NAT GW) |
