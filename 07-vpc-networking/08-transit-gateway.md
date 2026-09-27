# AWS Transit Gateway

## What is Transit Gateway?

**AWS Transit Gateway (TGW)** is a regional network hub that interconnects thousands of VPCs and on-premises networks through a single gateway. It uses a **hub-and-spoke architecture**, replacing the need for complex, full-mesh VPC peering.

**Analogy:** Transit Gateway is like a central train station — all routes pass through the hub, and you can reach any destination by connecting to the hub once.

---

## What Can Connect to Transit Gateway?

- **VPCs** (via VPC attachment)
- **Site-to-Site VPN connections**
- **Direct Connect Gateway** (via Transit VIF — enables DX to TGW)
- **Another Transit Gateway** (via TGW peering, for cross-region or cross-account)
- **SD-WAN / third-party appliances** (via Connect attachment)

---

## Why Transit Gateway Instead of VPC Peering?

| Scenario | VPC Peering | Transit Gateway |
|---|---|---|
| 5 VPCs full mesh | 10 peering connections | 1 TGW + 5 attachments |
| 100 VPCs full mesh | 4,950 peering connections | 1 TGW + 100 attachments |
| Transitive routing | NOT supported | Supported |
| On-premises connectivity | Separate VPN per VPC | Single VPN to TGW |
| Cross-account | Supported | Supported (via RAM sharing) |

---

## Transit Gateway Route Tables

TGW uses **route tables** to control which attachments can communicate with each other.

### Flat Network (all VPCs can talk to each other)
- One default route table with all VPC CIDRs propagated
- All attachments are associated with this route table

### Segmented Network (isolation)
- Create separate route tables for different environments (e.g., production vs development)
- Production VPCs route only to production route table
- Development VPCs cannot reach production VPCs
- Both can reach the shared services VPC (on-premises VPN route)

---

## TGW Advanced Features

### IP Multicast
- TGW supports IP Multicast — delivers a single stream to multiple receivers simultaneously
- Must be enabled at TGW creation time
- Uses UDP multicast group addresses (Class D: 224.0.0.0 to 239.255.255.255)

### AZ Affinity
- TGW attempts to keep traffic in the **originating AZ** to minimize inter-AZ data transfer costs
- If source and destination are in the same AZ, traffic stays in that AZ
- Enable **Appliance Mode** on the TGW attachment for centralized inspection VPCs to ensure symmetric routing (same ENI used for forward and return traffic)

### Resource Sharing (RAM)
- TGW can be shared across AWS accounts using **AWS Resource Access Manager (RAM)**
- All accounts attach their VPCs to a single, centrally managed TGW

---

## Centralized Architectures Using TGW

### Centralized Egress (internet access)
All VPCs route internet-bound traffic through a single Egress VPC with a NAT Gateway.

### Centralized VPC Endpoints
All VPCs share a single VPC that hosts Interface Endpoints for AWS services.

### Centralized Traffic Inspection
All traffic passes through a Security VPC with firewall appliances (GWLB + network appliances) via TGW routing.

---

## TGW vs Direct Connect Gateway

| | Direct Connect Gateway | Transit Gateway |
|---|---|---|
| Purpose | Connect a DX connection to multiple VPCs across regions | Hub for VPCs, VPN, and DX |
| VPC-to-VPC routing | Not supported (can't route VPC-to-VPC) | Supported |
| Connectivity | DX + Private VIFs or Transit VIF | VPC, VPN, DX via Transit VIF |
| Scale | Up to 10 VGWs | Thousands of attachments |

Use **DX Gateway with Transit VIF → TGW** for the scalable hybrid architecture.

---

## Key Points / Exam Tips

- TGW solves the **transitive routing** problem — VPC Peering is not transitive, TGW is
- TGW is **regional** — to connect VPCs in different regions, use TGW peering between regional TGWs
- TGW supports **Equal-Cost Multi-Path (ECMP)** routing with VPN connections — use this to exceed the 1.25 Gbps VPN single-tunnel limit (Virtual Private Gateway does NOT support ECMP)
- For **centralized inspection**: TGW + GWLB + Network Firewall/appliances in a Security VPC
- TGW can be shared across accounts using RAM — no need to create one per account

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Interconnect hundreds of VPCs" | Transit Gateway |
| "Hub-and-spoke network" | Transit Gateway |
| "VPN bandwidth > 1.25 Gbps" | TGW with ECMP (not Virtual Private Gateway) |
| "Centralized internet egress for all VPCs" | TGW + Egress VPC with NAT Gateway |
| "Centralized firewall inspection" | TGW + GWLB in Security VPC |
| "On-premises to multiple VPCs via Direct Connect" | DX Gateway + Transit VIF → TGW |
| "Share TGW across accounts" | AWS Resource Access Manager (RAM) |
| "Transitive routing" | Transit Gateway (Peering is NOT transitive) |
