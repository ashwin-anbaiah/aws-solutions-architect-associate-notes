# Route 53 Resolver (Hybrid DNS)

## What is Route 53 Resolver?

Every VPC includes a built-in **Route 53 Resolver** (also called the VPC DNS server):

- Runs at the **VPC Base + 2 address** (e.g., if VPC CIDR is `10.0.0.0/16`, the Resolver is at `10.0.0.2`)
- Resolves DNS queries from:
  - **Route 53 Private Hosted Zones** associated with the VPC
  - **Route 53 Public Hosted Zones**
  - **AWS-provided DNS** for services (e.g., `ec2.amazonaws.com`, S3 endpoints)
- Accessible only from **within the VPC** — cannot be queried from on-premises networks directly

## The Hybrid DNS Problem

When connecting on-premises networks to AWS via VPN or Direct Connect, DNS resolution across boundaries fails by default:

- **On-premises DNS servers** cannot query Route 53 Resolver (it's inside the VPC)
- **EC2 instances in VPC** cannot resolve on-premises hostnames (e.g., `server.corp.internal`)

**Solution**: Route 53 Resolver **Inbound and Outbound Endpoints**

## Resolver Inbound Endpoint

Allows **on-premises DNS servers** to forward DNS queries to Route 53 Resolver.

**Use case**: On-premises server needs to resolve `app.myapp.com` which is defined in a Route 53 Private Hosted Zone.

**How it works:**
1. Create a Route 53 **Inbound Resolver Endpoint** in your VPC (creates ENIs in selected subnets)
2. Inbound Endpoint gets private IPs in your VPC
3. Configure your on-premises DNS server to **forward** queries for `myapp.com` to those IPs
4. Route 53 Resolver handles the resolution and returns the answer

## Resolver Outbound Endpoint

Allows **VPC instances** to resolve DNS names from on-premises or external DNS systems.

**Use case**: EC2 instance in VPC needs to resolve `server.corp.internal` managed by an on-premises DNS server.

**How it works:**
1. Create a Route 53 **Outbound Resolver Endpoint** in your VPC (creates ENIs in selected subnets)
2. Create **Resolver Rules** (Conditional Forwarding Rules) that specify:
   - Which domain (e.g., `corp.internal`) should be forwarded
   - Which DNS server IP to forward to (e.g., on-premises DNS at `192.168.0.17`)
3. Route 53 Resolver forwards matching queries to the specified on-premises DNS server

## Hybrid DNS Architecture Summary

```
On-Premises                          AWS VPC
                                    +-----------+
Corp DNS Server  --[VPN/DX]--->     Inbound     -->  Route 53 Resolver
(192.168.0.17)                      Endpoint         --> Private Hosted Zone
                                    +-----------+
                                    +-----------+
VPC EC2 Instance --->               Outbound    --->  On-Prem DNS Server
                                    Endpoint         (192.168.0.17)
                                    +-----------+
```

## Resolver Rules (Forwarding Rules)

- **Forwarding Rules**: Define which domain names Route 53 Resolver should forward to specific DNS servers
- Example rule: `corp.internal → forward to 192.168.0.17`
- Rules can be **shared across accounts** using AWS Resource Access Manager (RAM)
- Can have multiple forwarding rules for different on-premises domains

## Key Points / Exam Tips

- Route 53 Resolver runs at **VPC Base+2** — this is the default DNS server for all VPC instances
- **Inbound Endpoint** = on-premises → AWS (on-prem DNS queries AWS private hosted zones)
- **Outbound Endpoint** = AWS → on-premises (VPC instances query on-prem DNS)
- Both require VPN or Direct Connect to link the networks
- **Resolver Rules** (Conditional Forwarding) tell Route 53 Resolver which domains to forward to which DNS servers
- Resolver endpoints create **ENIs** in your chosen subnets — apply Security Groups to control access

## Trigger Words

- "On-premises servers resolve Route 53 private hosted zone records" → Inbound Resolver Endpoint
- "EC2 instances resolve on-premises DNS names" → Outbound Resolver Endpoint + Forwarding Rule
- "Hybrid DNS" → Route 53 Resolver Inbound/Outbound Endpoints
- "DNS resolution across VPN/Direct Connect" → Route 53 Resolver Endpoints
