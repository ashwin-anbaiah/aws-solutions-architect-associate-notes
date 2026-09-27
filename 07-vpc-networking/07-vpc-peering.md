# VPC Peering

## What is VPC Peering?

**VPC Peering** is a direct, private network connection between two VPCs that allows resources in each VPC to communicate using private IP addresses — as if they were in the same network.

**Analogy:** VPC Peering is like building a private road between two properties so residents can visit each other directly, without going on the public highway.

---

## Key Properties

- **One-to-one** connection — each peering is between exactly two VPCs
- Supports connections **within the same region** (intra-region) and **across regions** (inter-region)
- Supports connections within the **same AWS account** or **across different AWS accounts**
- No bandwidth bottleneck — traffic uses AWS backbone network
- **VPCs must have non-overlapping CIDR blocks** — you cannot peer VPCs with conflicting IP ranges

---

## NOT Transitive

VPC Peering is **not transitive**:

```
VPC-A peered with VPC-B
VPC-B peered with VPC-C

Result: VPC-A CANNOT communicate with VPC-C via VPC-B
```

To connect VPC-A to VPC-C, you must create a **separate, direct peering connection** between them.

This becomes impractical at scale (N VPCs requires N*(N-1)/2 peering connections). Use **Transit Gateway** instead.

---

## Route Table Configuration Required

VPC Peering does not automatically configure routing. After creating a peering connection, you must:
1. Add a route in VPC-A's route table: destination = VPC-B's CIDR, target = peering connection ID (`pcx-xxxxxxxx`)
2. Add a route in VPC-B's route table: destination = VPC-A's CIDR, target = same peering connection

---

## VPC Peering vs. PrivateLink

| Feature | VPC Peering | PrivateLink (Interface Endpoint) |
|---|---|---|
| Connectivity type | Full Layer 3 (all resources) | Application-level (one service only) |
| Direction | Bidirectional | One-way (consumer to provider) |
| Overlapping CIDRs | NOT supported | Supported |
| Scale limit | 125 connections per VPC | No limit |
| Use case | Many resources communicating between two VPCs | Exposing one service to many consumers |

---

## VPC Peering Limits

- Max **125 active peering connections** per VPC
- For more than 125 VPCs or hub-and-spoke routing, use **Transit Gateway**

---

## Cross-Region VPC Peering

- Supports peering VPCs in **different AWS Regions**
- Traffic between regions travels over **AWS global backbone** (not the public internet)
- Incurs **inter-region data transfer charges**
- Security Groups can reference Security Group IDs from **same-region peered VPCs only** (cross-region peers must use IP ranges/CIDRs)

---

## Key Points / Exam Tips

- **VPC Peering is NEVER transitive** — this is one of the most tested facts about peering
- VPCs with **overlapping CIDRs cannot be peered**
- Always update **route tables on BOTH sides** after creating a peering connection
- Use VPC Peering when **many resources in two VPCs need to communicate** — use PrivateLink when exposing **a single service** to consumers
- There is no limit on bandwidth for VPC peering (unlike VPN connections)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Two VPCs need full private connectivity" | VPC Peering |
| "A → B peered, B → C peered, can A reach C?" | No — peering is not transitive |
| "VPCs with overlapping CIDRs need connectivity" | Use PrivateLink (peering not possible) |
| "More than 125 VPCs need hub-and-spoke routing" | Transit Gateway |
| "Private connectivity between two AWS accounts" | VPC Peering (cross-account) or PrivateLink |
| "Route table must be updated after peering" | True — peering does not auto-add routes |
