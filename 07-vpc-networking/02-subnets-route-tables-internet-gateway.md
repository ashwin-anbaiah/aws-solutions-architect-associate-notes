# Subnets, Route Tables, and Internet Gateway

## Subnets

A **subnet** is a range of IP addresses within your VPC. Subnets partition the VPC's address space.

Key properties:
- Each subnet lives in **exactly one Availability Zone**
- Subnets within a VPC can communicate with each other via the VPC local router (no configuration needed)
- A subnet is either **public** or **private** depending on its route table

---

## Public vs. Private Subnets

| Type | Route Table Contains | Internet Accessible? |
|---|---|---|
| **Public Subnet** | Route to Internet Gateway (0.0.0.0/0 → igw-xxx) | Yes (if instance also has a public IP) |
| **Private Subnet** | No route to Internet Gateway | No direct internet access |

**Rule:** A subnet is "public" if and only if its route table has a route to an Internet Gateway.

---

## Route Tables

A **Route Table** contains a set of rules (routes) that determine where network traffic from a subnet is directed.

| Destination | Target | Meaning |
|---|---|---|
| `10.0.0.0/16` | `local` | Traffic within the VPC stays local |
| `0.0.0.0/0` | `igw-xxxxxxxx` | All other traffic goes to the Internet Gateway (makes subnet public) |
| `0.0.0.0/0` | `nat-xxxxxxxx` | All other traffic goes to NAT Gateway (private subnet with internet access) |

### Main vs. Custom Route Tables
- Every VPC has one **Main Route Table** — all subnets use it by default
- You can create **Custom Route Tables** and associate them with specific subnets
- Best practice: leave the main route table as the private default; create custom route tables for public subnets

---

## Internet Gateway (IGW)

An **Internet Gateway** serves two purposes:
1. Provides a **target in the route table** for internet-bound traffic from public subnets
2. Performs **NAT (Network Address Translation)** for instances with public IPv4 addresses — translates private ↔ public IP

Key facts:
- Attach one IGW per VPC (one-to-one relationship)
- IGW is **horizontally scaled, redundant, and highly available** — no single point of failure
- Without an IGW, a VPC has no path to the public internet
- An instance in a public subnet must also have a **public IP (or Elastic IP)** assigned to communicate over the internet

---

## Enabling Internet Access: Checklist

For a public EC2 instance to reach the internet:
1. VPC has an Internet Gateway attached
2. Subnet route table has `0.0.0.0/0 → igw-xxx`
3. Instance has a public IPv4 address (or Elastic IP)
4. Security Group allows required outbound traffic
5. NACL (if customized) allows traffic

---

## Typical VPC Subnet Design

```
VPC: 10.0.0.0/16
|
|-- Availability Zone A
|   |-- Public Subnet:  10.0.0.0/24  (route: 0.0.0.0/0 → IGW)
|   |-- Private Subnet: 10.0.10.0/24 (route: 0.0.0.0/0 → NAT GW)
|
|-- Availability Zone B
    |-- Public Subnet:  10.0.1.0/24  (route: 0.0.0.0/0 → IGW)
    |-- Private Subnet: 10.0.11.0/24 (route: 0.0.0.0/0 → NAT GW)
```

---

## Key Points / Exam Tips

- A subnet can only belong to **one AZ** — it cannot span multiple AZs
- An IGW is **not** used for private subnet internet access — that's what NAT Gateway is for
- You **cannot associate more than one subnet** with the same Internet Gateway directly; it's a VPC-level attachment
- Route tables use **longest prefix matching** — more specific routes take priority over less specific ones
- Route table changes take effect **immediately** — no downtime
- **Bastion Host pattern**: place a hardened EC2 in a public subnet to act as a jump server for SSH into private subnet instances

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Public subnet" | Subnet with route to Internet Gateway |
| "Private subnet" | Subnet without route to Internet Gateway |
| "EC2 in private subnet needs internet access" | NAT Gateway (not IGW) |
| "Route all traffic to internet" | Add `0.0.0.0/0` route to route table |
| "Subnet that cannot reach the internet at all" | Private subnet with no 0.0.0.0/0 route |
| "Multiple subnets in one VPC in one AZ" | Allowed — but each subnet can only be in one AZ |
