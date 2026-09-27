# VPC Flow Logs and Traffic Mirroring

## VPC Flow Logs

**VPC Flow Logs** capture **metadata** about IP traffic flowing through your VPC network interfaces. They record details about each connection — but NOT the actual packet contents.

---

## Flow Log Scope

Flow logs can be enabled at three levels:
- **VPC level** — captures all traffic in the VPC
- **Subnet level** — captures all traffic in a specific subnet
- **ENI level** — captures traffic on a specific network interface

Flow logs also capture traffic from AWS-managed ENIs: ELB, RDS, ElastiCache, Redshift, Amazon WorkSpaces.

---

## Default Flow Log Format

Each log record contains:

```
version | account-id | interface-id | srcaddr | dstaddr | srcport | dstport | protocol | packets | bytes | start | end | action | log-status
```

Key fields:
- `srcaddr` / `dstaddr` — source and destination IP addresses
- `action` — ACCEPT or REJECT (useful for troubleshooting Security Group/NACL rules)
- `log-status` — OK, NODATA, SKIPDATA

---

## Flow Log Destinations

| Destination | Analysis Tool |
|---|---|
| **Amazon CloudWatch Logs** | CloudWatch Logs Insights (query/filter) |
| **Amazon S3** | Amazon Athena (SQL queries), cost-effective long-term storage |
| **Amazon Kinesis Data Firehose** | Near-real-time delivery to S3, Redshift, OpenSearch |

---

## Use Cases for Flow Logs

- Troubleshoot connectivity issues ("Why can't EC2-A talk to RDS?")
- Identify which Security Group or NACL rule is blocking traffic (REJECT entries)
- Detect anomalous or suspicious traffic patterns (high egress volume, unexpected IPs)
- Validate firewall rules and network configurations
- Cost analysis (identify high-bandwidth consumers)

**Performance impact:** Enabling Flow Logs has **no impact on network performance** — it is an out-of-band logging mechanism.

---

## VPC Traffic Mirroring

**VPC Traffic Mirroring** copies actual **packet payloads** (not just metadata) from an ENI and sends them to a target for analysis. This is distinct from Flow Logs which only capture metadata.

---

## Traffic Mirroring Components

| Component | Description |
|---|---|
| **Mirror Source** | ENI on the EC2 instance whose traffic you want to copy |
| **Mirror Target** | Destination for the mirrored traffic — another ENI or an NLB |
| **Mirror Filter** | Rules to select which traffic to mirror (by protocol, port, CIDR) |
| **Mirror Session** | Combines source + target + filter |

Traffic is encapsulated using **VXLAN** and sent to the target on **UDP port 4789**.

---

## Traffic Mirroring Use Cases

- **Deep Packet Inspection (DPI)** — analyze full packet content for threats
- **Intrusion Detection Systems (IDS)** — send traffic to Palo Alto, Check Point, Suricata
- **Advanced network troubleshooting** — inspect actual payload when metadata is insufficient
- **Forensics and compliance** — capture all traffic for investigation

---

## Flow Logs vs. Traffic Mirroring

| Feature | VPC Flow Logs | VPC Traffic Mirroring |
|---|---|---|
| Data captured | Metadata only (IPs, ports, action) | Full packet payload |
| Cost | Low | High |
| Performance impact | None | Moderate (copies actual traffic) |
| Use for | Connectivity troubleshooting, security monitoring | Deep packet inspection, forensics |
| Destination | S3 / CloudWatch / Firehose | ENI or NLB (UDP 4789) |
| Real-time analysis | Near-real-time (seconds to minutes) | Real-time (copied as it flows) |

---

## Key Points / Exam Tips

- Flow Logs do **NOT** capture packet payloads — only metadata (headers and flow statistics)
- Traffic Mirroring captures **actual packet content** — more powerful but higher cost and overhead
- Flow Logs have **no performance impact** on the network — safe to enable broadly
- Rejected traffic (NACL Deny or SG implicit deny) **does appear** in Flow Logs with action=REJECT
- Flow Log data is **not real-time** — there is a delay of several minutes before logs appear
- Traffic Mirroring source and destination can be in **different VPCs** (connected via peering or TGW) and even **different AWS accounts**

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Why is traffic being blocked between EC2 instances?" | VPC Flow Logs (check for REJECT actions) |
| "Capture all network traffic for forensic analysis" | Traffic Mirroring |
| "Deep packet inspection" | Traffic Mirroring |
| "Low-cost network monitoring" | VPC Flow Logs |
| "Send traffic to a third-party IDS appliance" | Traffic Mirroring |
| "No performance impact while monitoring" | VPC Flow Logs |
| "Full packet capture" | Traffic Mirroring |
