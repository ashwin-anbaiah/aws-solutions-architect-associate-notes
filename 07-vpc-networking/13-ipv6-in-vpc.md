# IPv6 in VPC

## IPv6 Basics

**IPv6** uses **128-bit addresses** (vs IPv4's 32-bit), providing an enormous address space. IPv6 addresses are **globally unique and publicly routable by default** — there is no concept of "private IPv6" the way RFC 1918 is private for IPv4.

Example IPv6 address: `2001:db8:1234:1a00::1`

---

## IPv6 in AWS VPC

| Property | Detail |
|---|---|
| **VPC CIDR** | `/56` prefix (2^72 addresses — assigned by AWS) |
| **Subnet CIDR** | `/64` prefix per subnet |
| **Assignment** | AWS assigns the IPv6 CIDR block (you cannot choose your own range) |
| **IPv4 disabled?** | No — IPv4 cannot be disabled in a VPC; dual-stack is the only mode |
| **Addresses are** | Globally unique and publicly routable |

---

## Dual-Stack Mode

VPCs support **dual-stack mode** — resources can communicate using both IPv4 and IPv6 (or only one, depending on configuration). This allows gradual migration from IPv4 to IPv6.

---

## Internet Access for IPv6

### Inbound and Outbound (Public Subnet)
Use the **Internet Gateway** — it supports both IPv4 and IPv6 traffic.
- Add `::/0 → igw-xxx` to the public subnet route table

### Outbound Only (Private Subnet with IPv6)
Use an **Egress-Only Internet Gateway**:
- IPv6 equivalent of a NAT Gateway
- Allows outbound IPv6 traffic from private instances
- **Blocks unsolicited inbound IPv6 connections** from the internet
- Must add `::/0 → eigw-xxx` to the private subnet route table

**Important:** NAT Gateway does NOT work with IPv6 — Egress-Only Internet Gateway is the correct solution.

---

## Route Table for IPv6

A dual-stack private subnet typically has:

| Destination | Target |
|---|---|
| `10.0.0.0/16` | local (IPv4 VPC traffic) |
| `2001:db8:1234:1a00::/56` | local (IPv6 VPC traffic) |
| `0.0.0.0/0` | NAT Gateway (IPv4 outbound) |
| `::/0` | Egress-Only Internet Gateway (IPv6 outbound) |

---

## Security Groups and NACLs with IPv6

- Security Groups support **separate IPv6 rules** using `::/0` as the CIDR
- NACLs support separate IPv4 and IPv6 rules
- You must **explicitly allow** IPv6 traffic — existing IPv4 rules do NOT automatically apply to IPv6

---

## VPC Endpoints and IPv6

- VPC Interface Endpoints support IPv6 in many regions
- Enables private IPv6 access to AWS services without going through the internet

---

## IPv6-Only Subnets

- AWS supports creating **IPv6-only subnets** — instances launched in these subnets get only IPv6 addresses
- Useful for applications designed purely for IPv6 or when avoiding IPv4 complexity

---

## When to Use IPv6

- **IoT workloads** — vast number of devices each needing a unique address
- **Global-scale applications** — no risk of running out of IP space
- **Avoid NAT bottlenecks** — every IPv6 device has a globally routable address, eliminating NAT overhead
- **Compliance** — some government/industry standards require IPv6 support
- **New greenfield architectures** — start with IPv6 to future-proof

---

## Key Points / Exam Tips

- **IPv4 cannot be disabled** in a VPC — dual-stack only
- IPv6 addresses are **always public** — there is no private IPv6 address space in AWS VPC
- Use **Egress-Only Internet Gateway** for outbound IPv6 from private subnets (NOT NAT Gateway)
- IPv6 CIDR is `/56` at VPC level and `/64` at subnet level
- VPC CIDR (IPv6) is **assigned by AWS** — you cannot choose your own IPv6 range
- Add `::/0 → Egress-Only IGW` for private subnet IPv6 outbound, and `::/0 → IGW` for public subnet IPv6

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Outbound-only IPv6 from private subnet" | Egress-Only Internet Gateway |
| "NAT for IPv6" | Does NOT exist — use Egress-Only IGW |
| "IPv6 address in VPC" | Globally unique, publicly routable, /64 per subnet |
| "IoT with millions of devices needing unique IPs" | IPv6 (no address exhaustion) |
| "Dual-stack VPC" | IPv4 + IPv6 simultaneously (IPv4 cannot be disabled) |
| "VPC IPv6 prefix" | /56 for VPC, /64 for subnet |
