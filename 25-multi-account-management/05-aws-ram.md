# AWS Resource Access Manager (RAM)

## What Is RAM?

**AWS Resource Access Manager (RAM)** allows you to share AWS resources with other AWS accounts — within your organization, a specific OU, or even accounts outside your organization. The key benefit: instead of duplicating resources in every account, you create them once and share the underlying resource.

---

## How RAM Works

```
Account A (Resource Owner)
  ├── Creates a VPC subnet
  └── Shares it via RAM → Account B, Account C

Account B and Account C
  └── Can launch EC2 instances INTO the shared subnet
      (they own their instances; Account A owns the network)
```

- The **resource owner** creates and manages the resource.
- **Consumers** use the resource but cannot modify or delete it.
- Sharing is free — RAM itself has no additional charge.

---

## Shareable Resources (Key Examples)

| Resource Type | Use Case |
|---|---|
| **VPC Subnets** | Share networking across accounts; EC2 in one account, VPC in another |
| **Transit Gateway** | Share a central TGW hub with other accounts |
| **Route 53 Resolver Rules** | Share DNS forwarding rules across accounts |
| **EC2 Dedicated Hosts** | Share BYOL-licensed hosts across accounts |
| **AWS License Manager configurations** | Manage software licenses centrally |
| **AWS CloudHSM clusters** | Share HSM for key management |
| **FSx for OpenZFS** | Share managed file systems |
| **Aurora DB Clusters** | Share databases across accounts |

---

## VPC Subnet Sharing — The Key Pattern

This is the most commonly tested RAM use case:

- A central **networking account** creates one VPC with subnets.
- RAM shares the subnets with other accounts in the same Organization.
- **Other accounts launch EC2 instances directly into those shared subnets.**
- All instances are in the **same VPC** — they communicate privately with zero extra routing config.
- Each account owns and manages its own EC2 instances.
- The networking account owns and manages the VPC, subnets, route tables, and security groups.

**Why this is cheapest for multi-account same-region private communication:**
- No Transit Gateway cost (hourly attachment fees + data processing fees)
- No VPC Peering overhead (no per-connection management)
- RAM sharing is free
- Single VPC = native private communication

---

## RAM vs Other Multi-Account Networking Options

| Option | Cost | Scalability | Use When |
|---|---|---|---|
| **RAM shared subnets** | Free | Same-region, same-org only | Best for same-region org-wide private networking |
| **VPC Peering** | Free (but operational overhead) | Poor at scale (full mesh explodes) | Simple, small number of VPC pairs |
| **Transit Gateway** | Paid (per attachment + data) | Scales to hundreds of VPCs | Large-scale, cross-region, many VPCs |
| **AWS PrivateLink** | Paid (per endpoint + data) | Service-to-service, not general networking | Exposing a specific service privately |

---

## Key Points / Exam Tips

- RAM is **free** — no charge for the sharing mechanism itself.
- Shared VPC subnets: multiple accounts in the **same VPC** = native private communication, zero extra cost.
- Resource **ownership stays with the creator** — consumers cannot modify shared resources.
- RAM can share with accounts **outside** your organization too (requires explicit invitation acceptance).
- Within an organization, sharing can be done without requiring an invitation if sharing within the org is enabled.
- The Transit Gateway can be shared via RAM — useful for spoke-VPC accounts that need to attach to a central hub.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Multiple accounts in same region/org, cheapest private EC2 communication" | RAM + shared VPC subnets |
| "Share a VPC subnet with another AWS account" | AWS RAM |
| "Share Transit Gateway across accounts" | AWS RAM |
| "Share Route 53 Resolver rules across accounts" | AWS RAM |
| "One VPC, multiple accounts, same network, no peering cost" | RAM shared subnets |
