# VPC Interface Endpoints and AWS PrivateLink

## What is a VPC Interface Endpoint?

A **VPC Interface Endpoint** (powered by AWS PrivateLink) creates a private IP address (via an **Elastic Network Interface / ENI**) inside your VPC that maps to an AWS service or a customer-hosted service. Traffic to the service goes over the AWS private network, never touching the public internet.

---

## Interface Endpoint vs. Gateway Endpoint

| Feature | Gateway Endpoint | Interface Endpoint |
|---|---|---|
| Supported services | S3 and DynamoDB only | 100+ AWS services + custom services |
| Mechanism | Route table entry | ENI with private IP in your subnet |
| Security Group | Not applicable | Yes — inbound rules required |
| Cost | Free | Hourly charge + per-GB data |
| Access from on-premises | No | Yes (via VPN or Direct Connect) |
| Access from peered VPCs | No | Yes |
| DNS | Prefix list in route table | Private DNS with regional/zonal hostnames |

---

## How Interface Endpoints Work

1. Create a VPC Interface Endpoint for a service (e.g., SQS, SNS, EC2 API)
2. AWS creates an **ENI** with a private IP in your selected subnet(s)
3. A **Security Group** is attached to the endpoint ENI — configure inbound rules (e.g., HTTPS 443 from your VPC CIDR)
4. Traffic from your VPC resources to the service goes through the ENI → AWS PrivateLink → service

---

## DNS Options for Interface Endpoints

Each Interface Endpoint gets DNS names:
- **Regional DNS**: `vpce-0b7d2995...sqs.us-east-1.vpce.amazonaws.com`
- **Zonal DNS**: `vpce-0b7d2995...-us-east-1a.sqs.us-east-1.vpce.amazonaws.com`

### Private DNS Setting
- When **Private DNS is enabled**, the public hostname of the AWS service (e.g., `sqs.us-east-1.amazonaws.com`) resolves to the **private endpoint IP** within your VPC
- This means applications do NOT need code changes — they continue to use the standard AWS service URL and automatically get the private route
- VPC must have **"Enable DNS hostnames"** and **"Enable DNS Support"** set to `true`

---

## VPC Endpoint Service (PrivateLink for Custom Services)

You can expose **your own application** privately to other VPCs using AWS PrivateLink:

**Provider VPC Setup:**
1. Deploy your application behind an NLB
2. Create a VPC Endpoint Service pointing to the NLB
3. Share the service name with consumers

**Consumer VPC Setup:**
1. Create a VPC Interface Endpoint pointing to the provider's service name
2. AWS routes traffic through PrivateLink to the NLB

Benefits of PrivateLink for custom services:
- **Supports overlapping CIDRs** between consumer and provider VPCs (unlike VPC Peering)
- Can serve **thousands of consumer VPCs** (no peering limit)
- One-way: only consumer can originate traffic → provider (not bidirectional like VPC Peering)

---

## Accessing Interface Endpoints from Remote Networks

Unlike Gateway Endpoints, Interface Endpoints **can be accessed from**:
- On-premises networks (via VPN or Direct Connect)
- Peered VPCs
- Transit Gateway-connected networks

This makes them the solution for on-premises S3 access via a private path:
```
On-premises → Direct Connect → VPC → Interface Endpoint for S3 → S3 (private)
```

---

## Endpoint Policy

Interface Endpoints support resource-based policies to restrict:
- Which AWS service resources are accessible (e.g., specific SQS queue)
- Which IAM principals can use the endpoint
- Default policy: grants full access

---

## Key Points / Exam Tips

- Interface Endpoints cost money (hourly + per-GB) — use Gateway Endpoints for S3/DynamoDB where possible
- **Private DNS must be enabled** for seamless use without changing application code
- VPC must have "Enable DNS Hostnames" and "Enable DNS Support" = true for Private DNS to work
- Interface Endpoints have Security Groups — inbound HTTPS (443) from the VPC CIDR is the standard rule
- For **HA**: create Interface Endpoints in multiple AZs (one ENI per AZ)
- PrivateLink supports overlapping CIDRs — use it when VPC Peering is not possible due to CIDR conflicts

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Private access to SQS / SNS / Kinesis / EC2 API from VPC" | Interface Endpoint (PrivateLink) |
| "On-premises access to S3 or any AWS service over private network" | Interface Endpoint (not Gateway Endpoint) |
| "Expose your service to consumers in other VPCs without peering" | VPC Endpoint Service (PrivateLink) |
| "Service provider and consumer have overlapping CIDRs" | PrivateLink (works; VPC Peering does NOT work with overlapping CIDRs) |
| "Application uses standard AWS SDK URL but traffic stays private" | Interface Endpoint with Private DNS enabled |
| "Thousands of VPCs need to access a service" | PrivateLink (no limit vs 125 peering connections) |
