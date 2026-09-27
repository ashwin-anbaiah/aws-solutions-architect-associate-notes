# Amazon Detective

## What is Amazon Detective?

**Amazon Detective** uses **machine learning, statistical analysis, and graph technology** to help security teams **investigate and identify the root cause** of security issues or suspicious activities across AWS resources.

> "Detective answers 'what happened and why?' — it's the investigation tool, not the detection tool."

## The Problem Detective Solves

Security services like GuardDuty and Security Hub are excellent at **detecting and alerting** on threats. But when an alert fires, investigating the root cause requires manually correlating data across:
- CloudTrail API logs
- VPC Flow Logs
- GuardDuty findings
- Security Hub findings
- Amazon Macie findings

**Detective automates this correlation** — it builds a **behavior graph** using ML to connect related events and entities, making investigation faster and more accurate.

## How Detective Works

### Data Sources

Detective automatically ingests and correlates data from:

| Source | What It Provides |
|---|---|
| **VPC Flow Logs** | Network traffic patterns between resources |
| **AWS CloudTrail Events** | API activity, user actions, service calls |
| **Amazon GuardDuty Findings** | Threat detections linked to associated activity |
| **Amazon Security Hub Findings** | Aggregated security findings |
| **Amazon Macie Findings** | Sensitive data access events |
| **EKS Audit Logs** | Kubernetes API activity |

> Unlike GuardDuty (which accesses data sources natively), Detective requires these logs to exist and pulls them for ML analysis.

### Behavior Graph

- Detective builds a **behavior graph** — a time-ordered graph of entity relationships and activities
- Entities: IAM users, roles, EC2 instances, IP addresses, AWS accounts
- Enables visual exploration of "who talked to whom, when, and how"

### Investigation Workflow

```
GuardDuty finding: EC2 cryptocurrency mining
        ↓  Click "Investigate in Detective"
Amazon Detective
        ↓  Shows behavior graph
  - EC2 instance activity timeline
  - All IPs this instance communicated with
  - API calls made by the instance's role
  - Network connections before and after the finding
  - Related findings
        ↓
Root cause identified: misconfigured Lambda function executed malicious script
```

## Detective vs GuardDuty vs Security Hub

| Service | Role | Question It Answers |
|---|---|---|
| **GuardDuty** | Threat detection | "Is something suspicious happening?" |
| **Security Hub** | Findings aggregation + CSPM | "What are all my security findings?" |
| **Amazon Detective** | Investigation | "Why did this happen and what was the blast radius?" |

These services are **complementary** — designed to work together:
- GuardDuty detects → Security Hub aggregates → Detective investigates

## Multi-Account Support

- Detective supports **multi-account** operation via AWS Organizations
- An administrator account can investigate activity across all member accounts
- Provides a unified behavior graph across the organization

## Key Points / Exam Tips

- Detective = **investigation tool** — it does not generate alerts (GuardDuty does)
- Uses **ML + graph analysis** to correlate events across VPC Flow Logs, CloudTrail, and GuardDuty
- Enables root cause analysis when a security finding fires
- Integrates with GuardDuty — you can click "Investigate in Detective" from a GuardDuty finding
- Detective builds a **behavior graph** over time — it improves as more data is collected
- Requires enabling Detective (it's not on by default); begins building the graph automatically after enablement

## Trigger Words

| Keyword | Think |
|---|---|
| "Investigate root cause of security incident" | Amazon Detective |
| "ML-based security investigation" | Amazon Detective |
| "Correlate CloudTrail + VPC Flow Logs + GuardDuty" | Amazon Detective |
| "Behavior graph for security analysis" | Amazon Detective |
| "What happened after a GuardDuty finding?" | Amazon Detective |
| "Find blast radius of compromised credential" | Amazon Detective |

---

## Security Services Quick Reference

| Service | Layer | Key Function |
|---|---|---|
| **WAF** | L7 | Block HTTP/web exploits (SQLi, XSS, rate limiting) |
| **Shield Standard** | L3/L4 | Free, always-on DDoS protection |
| **Shield Advanced** | L3/L4 | Enhanced DDoS + DRT access + cost protection |
| **Network Firewall** | L3-L7 | Stateful VPC firewall with IDS/IPS (Suricata) |
| **Firewall Manager** | Management | Centrally enforce WAF/Shield/NF across org |
| **Inspector** | Vulnerability | CVE scanning for EC2/Lambda/ECR |
| **GuardDuty** | Threat Detection | ML-based threat detection from logs |
| **Security Hub** | Aggregation | Centralize all findings + CSPM |
| **Detective** | Investigation | Root cause analysis via behavior graph |
