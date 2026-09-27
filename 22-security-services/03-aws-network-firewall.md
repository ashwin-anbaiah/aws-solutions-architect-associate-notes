# AWS Network Firewall

## What is AWS Network Firewall?

**AWS Network Firewall** is a **stateful, managed network firewall and intrusion detection/prevention service (IDS/IPS)** for **Amazon VPCs**.

> "Network Firewall = deep packet inspection and IDS/IPS for your VPC traffic — beyond what Security Groups and NACLs can do."

## What Network Firewall Provides

| Capability | Description |
|---|---|
| **Stateful inspection** | Tracks TCP/UDP connection state; allows return traffic automatically |
| **Stateless rules** | Simple, fast header-based matching (like NACLs) |
| **Intrusion Prevention (IPS)** | Block known threats based on Suricata rules |
| **Intrusion Detection (IDS)** | Detect and log threats without blocking |
| **Domain filtering** | Allow/deny traffic to specific domain names or FQDNs |
| **Protocol detection** | Identify application protocols (HTTP, TLS, DNS, etc.) |
| **TLS inspection** | Inspect encrypted HTTPS traffic (with decryption) |

## Where Network Firewall Sits

Network Firewall is deployed in a **dedicated firewall subnet** within the VPC. All traffic is routed through a **firewall endpoint** in that subnet.

```
Internet → Internet Gateway
        → Route table routes traffic → Firewall Subnet (Network Firewall Endpoint)
                                    → Route table routes to → Application Subnet (EC2/ECS)
```

- The firewall inspects traffic in both directions (inbound and outbound)
- Requires updating **VPC route tables** to direct traffic through the firewall endpoint

## Key Components

### Firewall Policy

- Defines the **stateless and stateful rule groups** applied to traffic
- Specifies default actions for traffic not matching any rule

### Stateless Rules

- Fast-path rules evaluated first
- Match on: protocol, source/destination IP, source/destination port
- Actions: Pass, Drop, or Forward to stateful engine
- Similar to NACLs but more flexible

### Stateful Rules

- Evaluated after stateless rules
- Track connection state (can allow established connections without explicit return rules)
- Actions: Pass, Drop, Alert
- Support **Suricata-compatible rules** (open-source IDS/IPS rule format)

### Suricata Rules

- **Suricata** is an open-source network threat detection engine
- AWS Network Firewall supports the **Suricata rule language**
- Allows importing community or commercial Suricata rulesets
- Pattern matching on payload content, protocol anomalies, known attack signatures

### Domain Lists (Stateful)

- Block or allow traffic to/from specific **fully qualified domain names (FQDNs)**
- Example: block all outbound traffic except `*.amazonaws.com` and `mycompany.com`
- Works for HTTP (via `Host` header) and HTTPS (via SNI — Server Name Indication)

## Network Firewall vs Security Groups vs NACLs vs WAF

| Feature | Security Group | NACL | Network Firewall | WAF |
|---|---|---|---|---|
| Scope | EC2 instance level | Subnet level | VPC-wide | Resource-specific (ALB, CF) |
| Stateful | Yes | No | Yes (+ stateless) | Yes |
| IDS/IPS | No | No | **Yes** | No |
| Domain filtering | No | No | **Yes** | No |
| Deep packet inspection | No | No | **Yes** | Limited (L7 HTTP) |
| Layer | L3/L4 | L3/L4 | L3/L4/L7 | L7 (HTTP) |

## Logging

Network Firewall logs to:
- **Amazon S3**
- **Amazon CloudWatch Logs**
- **Amazon Kinesis Data Firehose** → OpenSearch/Redshift/S3

Log types: **Alert** (matched drop/alert rules), **Flow** (all connection metadata)

## Key Points / Exam Tips

- Network Firewall = **stateful IDS/IPS** for VPCs — more powerful than SGs/NACLs
- Uses **Suricata rules** for threat signatures — industry-standard format
- Supports **domain filtering** (allow/block by FQDN) — useful for egress control
- Deployed in a **dedicated firewall subnet**; VPC route tables must route through it
- Managed by **AWS Firewall Manager** for centralized multi-account deployment
- Network Firewall protects **east-west** and **north-south** traffic within a VPC
- For "stateful VPC-level firewall" or "IPS/IDS in the VPC" → Network Firewall

## Trigger Words

| Keyword | Think |
|---|---|
| "Stateful IDS/IPS for VPC" | AWS Network Firewall |
| "Block traffic to specific domains" | Network Firewall domain filtering |
| "Suricata rules in AWS" | AWS Network Firewall |
| "Deep packet inspection in VPC" | AWS Network Firewall |
| "Network-level threat detection" | AWS Network Firewall |
| "Firewall beyond security groups" | AWS Network Firewall |
