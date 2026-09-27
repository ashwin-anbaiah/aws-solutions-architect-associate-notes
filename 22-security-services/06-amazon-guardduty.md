# Amazon GuardDuty

## What is Amazon GuardDuty?

**Amazon GuardDuty** is an **intelligent threat detection service** that continuously monitors AWS accounts and workloads for malicious activity and unauthorized behavior.

> "GuardDuty = 'one click' threat detection using machine learning — no agents, no data to manage."

## How GuardDuty Works

GuardDuty analyzes **billions of events** from multiple AWS data sources using:
- **Machine Learning (ML)** — identifies anomalous behavior
- **Threat Intelligence** — compares against known malicious IPs, domains, and signatures
- **Anomaly Detection** — baselines normal behavior and flags deviations

## Data Sources GuardDuty Analyzes

| Source | What It Detects |
|---|---|
| **AWS CloudTrail Events** | Unusual API calls, unauthorized deployments, credential abuse |
| **VPC Flow Logs** | Unusual internal traffic, unusual IP addresses, port scanning |
| **DNS Query Logs** | Compromised EC2 sending encoded data in DNS queries, C&C communication |
| **S3 Data Events** | Unusual S3 access patterns (optional data source) |
| **EKS Audit Logs** | Suspicious Kubernetes activity (optional) |
| **RDS Login Events** | Unusual database login patterns (optional) |
| **Lambda Network Activity** | Unusual Lambda network behavior (optional) |

> GuardDuty does **not** require you to configure or export these logs — it accesses them directly.

## Example Threats GuardDuty Detects

| Category | Examples |
|---|---|
| **EC2** | Cryptocurrency/Bitcoin mining, port scanning, RDP/SSH brute force, C&C callbacks |
| **IAM** | Root credential usage, anomalous API behavior, Kali Linux tool usage |
| **S3** | Malicious IP reading from S3, public access re-enabled, bucket policy changes |
| **EC2 Network** | Unusual traffic volume, communication with known malicious IPs |

## GuardDuty Findings

- Each detected threat generates a **Finding**
- Findings include: threat type, severity, affected resource, evidence
- Severity levels: **Critical, High, Medium, Low, Informational**

### Finding Destinations

| Destination | Purpose |
|---|---|
| **Amazon EventBridge** | Trigger automated responses (Lambda, SSM Automation, SNS) |
| **AWS Security Hub** | Centralized security findings aggregation |
| **SIEM / Partner Solutions** | Export via Firehose to third-party tools |

## Multi-Account Support

- GuardDuty supports **multi-account management** via AWS Organizations
- A designated **administrator account** sees findings from all member accounts
- New member accounts automatically have GuardDuty enabled

## GuardDuty vs Inspector vs CloudTrail

| Service | What It Does |
|---|---|
| **GuardDuty** | Detects active threats and anomalous behavior from logs |
| **Inspector** | Finds vulnerabilities (CVEs) in software and configuration |
| **CloudTrail** | Records API activity (audit log — not threat detection) |

## Key Points / Exam Tips

- GuardDuty = **intelligent threat detection** using ML and threat intelligence
- Enable with **one click** — no agents, no log configuration required
- Analyzes: **CloudTrail logs, VPC Flow Logs, DNS logs** (core sources — know these)
- Findings are published to **EventBridge** for automated response
- Common finding: EC2 instance doing **cryptocurrency mining** or communicating with known malicious IP
- **Root credential usage** = GuardDuty finding (IAM category)
- Multi-account: administrator account manages all findings via Organizations
- GuardDuty does **not** block traffic — it detects and alerts; remediation is external

## Trigger Words

| Keyword | Think |
|---|---|
| "Detect cryptocurrency mining on EC2" | Amazon GuardDuty |
| "Intelligent threat detection, one click" | Amazon GuardDuty |
| "Analyze VPC Flow Logs for threats" | Amazon GuardDuty |
| "Machine learning threat detection" | Amazon GuardDuty |
| "Compromised instance calling C&C server" | GuardDuty finding |
| "Root account used — alert security team" | GuardDuty + EventBridge + SNS |
