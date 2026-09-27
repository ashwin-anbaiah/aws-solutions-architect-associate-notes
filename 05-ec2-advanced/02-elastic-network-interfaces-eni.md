# Elastic Network Interfaces (ENI)

## What Is an ENI?

An **Elastic Network Interface (ENI)** is a logical virtual network card in a VPC. Every EC2 instance has at least one ENI (the primary ENI, `eth0`). Additional ENIs can be created and attached to instances.

Think of an ENI as the network port on a physical server — it has a MAC address, private IPs, and optionally a public IP. The difference is that ENIs in AWS are software-defined and can be detached and moved.

---

## ENI Attributes

Each ENI can have:

| Attribute | Details |
|---|---|
| **Primary private IPv4** | Mandatory — assigned from the subnet CIDR |
| **Secondary private IPv4(s)** | Optional additional private IPs |
| **One Elastic IP per private IP** | Optional static public IP |
| **One public IPv4** | Optional auto-assigned public IP |
| **One or more Security Groups** | Each ENI has its own security groups |
| **A MAC address** | Unique per ENI, persists with the ENI (important for software licensing) |

---

## Primary ENI vs Secondary ENI

- **Primary ENI (eth0)**: Created automatically when the instance launches. Cannot be detached from a running instance.
- **Secondary ENIs**: Created separately or attached at launch. Can be **detached and re-attached** to other instances in the same AZ.

**Key constraint**: ENIs are **AZ-scoped** — you cannot move an ENI across Availability Zones. An ENI in AZ1 can only be attached to an instance in AZ1.

---

## ENI Use Cases

### 1. High Availability / Failover Within an AZ

If your primary instance fails:
1. Detach the ENI (with its private IP) from the failed instance
2. Attach that same ENI to a hot standby instance in the same AZ

From the network's perspective, the private IP is now on a new instance — no DNS update required, no IP change. This is faster than creating a new instance and updating routing.

```
Before: EC2-Primary (10.10.0.15) ← ENI ← Clients
After failure: EC2-Standby (10.10.0.15) ← same ENI ← same Clients
                                    (clients see no change in IP)
```

### 2. Static IP / MAC Address Retention

- Some software licenses are tied to a **MAC address** (the ENI's MAC persists even across different instances)
- By keeping the ENI and moving it to a new instance, the MAC address stays the same — no license re-registration needed

### 3. Multi-Homed Instances (Multiple Network Interfaces)

Attach multiple ENIs to a single instance, each connected to a different subnet or security group:

- **eth0**: Connected to a public subnet — serves external traffic (web tier)
- **eth1**: Connected to a private subnet — communicates with databases (db tier)
- **eth2**: Connected to a management subnet — admin access, monitoring

This segregates traffic types. Each ENI can have its own security group rules, so the database subnet access and public access are controlled independently.

### 4. Network Security Appliance Pattern

In a hub-and-spoke architecture, a centralized security appliance (firewall, IDS) needs to be on multiple network paths. Multiple ENIs let you attach the appliance to different subnets without routing through a single network path.

---

## ENI vs Elastic IP

| | ENI | Elastic IP |
|---|---|---|
| What it is | A virtual NIC (network card) | A static public IP address |
| Scope | AZ-specific | Region-specific (assigned to your account) |
| Can be moved | Yes — to instances in same AZ | Yes — to any instance or ENI in the Region |
| Has a MAC address | Yes | No |
| Contains IPs | Yes (private, optionally public) | It IS an IP address (attached to an ENI/instance) |

An Elastic IP is attached *to* an ENI. The ENI is the network card; the Elastic IP is the IP on that card.

---

## Key Points / Exam Tips

- **ENIs are AZ-scoped** — cannot move across AZs
- **Primary ENI cannot be detached** from a running instance
- **Secondary ENIs can be detached and moved** — key for failover patterns
- **MAC address persists with the ENI** — important for MAC-based software licensing
- **Each ENI has its own security groups** — useful for multi-homed/multi-tier instances
- **For cross-AZ failover, use other mechanisms** (Elastic IP re-mapping, Route 53 failover routing, ALB) — ENIs can't cross AZs

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Move a private IP to a standby instance in same AZ" | Detach ENI and attach to standby |
| "Software licensing tied to MAC address" | ENI (MAC persists with the ENI) |
| "Instance connected to two subnets simultaneously" | Multi-homed instance with multiple ENIs |
| "Network appliance on multiple network paths" | Multiple ENIs on the appliance instance |
| "ENI across availability zones" | NOT possible — ENIs are AZ-scoped |
