# Site-to-Site VPN and VPN CloudHub

## What is AWS Site-to-Site VPN?

**AWS Site-to-Site VPN** creates an **IPSec encrypted tunnel** between your on-premises network and your AWS VPC. Traffic flows over the **public internet** but is encrypted at Layer 3.

---

## Components

| Component | Location | Role |
|---|---|---|
| **Virtual Private Gateway (VGW)** | AWS side (attached to VPC) | AWS endpoint of the VPN connection |
| **Customer Gateway (CGW)** | On-premises side | AWS resource representing your on-premises device/IP |
| **VPN Connection** | The encrypted tunnel between VGW and CGW |

---

## Key Properties

- **Protocol**: IPSec (Layer 3 encryption)
- **Tunnels**: 2 VPN tunnels per connection (for HA — if one tunnel fails, traffic fails over to the other)
- **Bandwidth**: ~**1.25 Gbps** per tunnel (single-tunnel limit)
- **Routing**: Supports **Static Routing** (manual) and **Dynamic Routing (BGP)**
- **Setup time**: Minutes (quick to provision — no physical provisioning needed)
- **Cost**: Hourly charge per VPN connection + data transfer

---

## VPN Architectures

### Single Connection
```
On-premises Router (CGW) ←→ VGW ←→ VPC
2 tunnels for HA within the connection
```

### Redundant (Primary + Failover)
```
On-premises Router 1 (CGW-1) → VGW → VPC (primary)
On-premises Router 2 (CGW-2) → VGW → VPC (failover)
```

### High Bandwidth: TGW + ECMP
To exceed the 1.25 Gbps per-connection limit:
- Use **Transit Gateway** as the AWS endpoint (not VGW)
- Enable **ECMP (Equal-Cost Multi-Path)** routing on the TGW
- Multiple VPN connections add aggregate bandwidth

**Note:** Virtual Private Gateway (VGW) does NOT support ECMP — only TGW does.

---

## VPN CloudHub

**VPN CloudHub** allows **multiple on-premises sites to communicate with each other** through a single VGW (used in "detached" mode — not attached to a VPC).

Requirements:
- Each site must have a Customer Gateway with a **unique BGP ASN**
- Sites must not have overlapping IP ranges
- Supports up to **10 Customer Gateways**
- Can act as a failover path between on-premises locations

```
Data Center (Branch A) ←→
Data Center (Branch B) ←→ VGW (hub) — sites communicate through the hub
Data Center (Branch C) ←→
```

---

## Site-to-Site VPN vs. Direct Connect

| Feature | Site-to-Site VPN | Direct Connect |
|---|---|---|
| Connection type | Encrypted tunnel over internet | Dedicated physical link |
| Setup time | Minutes | Weeks to months |
| Bandwidth | Up to ~1.25 Gbps per tunnel | 50 Mbps to 100 Gbps |
| Latency | Variable (internet) | Consistent and low |
| Encryption | Yes (IPSec by default) | No (need VPN over DX for encryption) |
| Cost | Lower (no physical provisioning) | Higher (port charges) |
| Use when | Quick setup, moderate bandwidth, cost-sensitive | High bandwidth, consistent latency, large data |

---

## DX + VPN as Backup

A common architecture uses Direct Connect as the primary path and Site-to-Site VPN as the failover:
- DX provides fast, consistent connectivity
- VPN activates automatically if DX fails
- BGP routing is used to prefer DX over VPN

---

## Key Points / Exam Tips

- Site-to-Site VPN traffic **goes over the public internet** but is **encrypted with IPSec**
- A single VPN connection caps at **~1.25 Gbps** — for more bandwidth, use TGW + ECMP
- VGW **does NOT support ECMP** — for aggregate bandwidth from multiple VPN connections, use Transit Gateway
- VPN CloudHub allows **branch-to-branch** communication — not just branch-to-AWS
- For quick, temporary, or low-bandwidth connectivity: VPN. For sustained high-bandwidth: Direct Connect
- **2 VPN tunnels** are always created per connection for HA — both are active/passive or active/active depending on configuration

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Encrypted connection between on-premises and AWS" | Site-to-Site VPN (or DX + VPN for encryption over DX) |
| "Quick setup in minutes" | Site-to-Site VPN (not Direct Connect) |
| "Multiple branch offices communicate through AWS" | VPN CloudHub |
| "VPN bandwidth > 1.25 Gbps" | Transit Gateway + ECMP + multiple VPN connections |
| "VGW with ECMP" | Invalid — VGW does NOT support ECMP (use TGW) |
| "Primary path DX, failover VPN" | DX + Site-to-Site VPN backup architecture |
