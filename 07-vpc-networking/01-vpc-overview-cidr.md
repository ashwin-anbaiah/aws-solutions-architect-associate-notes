# VPC Overview and CIDR

## What is a VPC?

A **Virtual Private Cloud (VPC)** is your own isolated, private network within AWS. It is a logically isolated section of the AWS cloud where you can launch AWS resources in a virtual network that you define.

Think of a VPC as your private data center inside AWS — you control the IP address space, subnets, routing, and access controls.

---

## VPC Key Properties

- **Regional service** — a VPC spans all Availability Zones within a single AWS Region
- You control the **IP address range** using CIDR notation
- Resources inside the VPC communicate using **private IP addresses** by default
- A VPC has a **local router** that automatically routes traffic between subnets within the VPC
- Multiple VPCs can exist in one AWS account

---

## CIDR (Classless Inter-Domain Routing)

CIDR is the IP addressing scheme that defines how many IPs a network block contains.

**Format:** `IP_address / prefix_length`

Example: `10.0.0.0/16`
- The `/16` means the first 16 bits are fixed (network address)
- The remaining 16 bits are available for host addresses
- Total IPs = 2^(32-16) = 2^16 = **65,536 addresses**

### CIDR Calculation Formula
```
Total addresses = 2 ^ (32 - prefix)

/16 = 2^16 = 65,536 IPs
/24 = 2^8  = 256 IPs
/28 = 2^4  = 16 IPs
```

### Common CIDR Sizes

| CIDR | Total IPs | Common Use |
|---|---|---|
| /16 | 65,536 | VPC CIDR (large) |
| /24 | 256 | Subnet |
| /28 | 16 | Small subnet (minimum subnet size in AWS) |
| /32 | 1 | Single specific IP address |
| /0 | All IPs | Entire internet |

---

## VPC IPv4 Addressing

AWS VPC supports CIDR ranges from **/16** (65,536 IPs) to **/28** (16 IPs).

**RFC 1918 private IP ranges (recommended):**

| Range | AWS Recommended VPC CIDR |
|---|---|
| 10.0.0.0/8 (10.0.0.0 – 10.255.255.255) | 10.X.0.0/16 |
| 172.16.0.0/12 (172.16.0.0 – 172.31.255.255) | 172.16.0.0/16 to 172.31.0.0/16 |
| 192.168.0.0/16 (192.168.0.0 – 192.168.255.255) | 192.168.0.0/16 |

---

## VPC IPv6 Addressing

- IPv6 CIDR is **/56** at VPC level (2^72 addresses — essentially unlimited)
- Each subnet gets a **/64** prefix
- IPv6 addresses are **globally unique and publicly routable** by default
- IPv6 is assigned by AWS (you cannot choose the range like you can for IPv4)
- **IPv4 cannot be disabled** — dual-stack (IPv4 + IPv6) is the only supported mode

---

## AWS Reserved IPs in Every Subnet

AWS reserves **5 IP addresses** in every subnet (cannot be used for instances):

| Reserved IP | Purpose |
|---|---|
| First IP (x.x.x.0) | Network address |
| Second IP (x.x.x.1) | VPC router |
| Third IP (x.x.x.2) | AWS DNS server |
| Fourth IP (x.x.x.3) | Reserved for future use |
| Last IP (x.x.x.255) | Broadcast address |

So a /24 subnet (256 total) has only **251 usable** IP addresses.

---

## Key Points / Exam Tips

- VPC is **regional** — one VPC cannot span multiple regions (use VPC Peering or Transit Gateway for cross-region connectivity)
- VPC CIDR range must be between **/16 and /28**
- VPCs with **overlapping CIDRs cannot be peered**
- The primary CIDR block of a VPC cannot be changed after creation — you can only **add secondary CIDR blocks**
- A VPC automatically has a **main route table** — all subnets use it by default unless you associate a custom route table
- **Default VPC**: every AWS account has a default VPC in each region with a 172.31.0.0/16 CIDR — it has a pre-configured internet gateway and public subnets for easy access

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Private network in AWS" | VPC |
| "Define IP address range for your AWS resources" | VPC with CIDR |
| "Overlapping CIDRs" | Cannot peer these VPCs |
| "Single IP address" | /32 CIDR |
| "Entire internet" | 0.0.0.0/0 or ::/0 (IPv6) |
| "How many IPs in a /24 subnet?" | 256 total, 251 usable (5 reserved by AWS) |
