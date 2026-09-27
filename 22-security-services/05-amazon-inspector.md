# Amazon Inspector

## What is Amazon Inspector?

**Amazon Inspector** is a **vulnerability management service** that continuously scans AWS workloads for software vulnerabilities and unintended network exposure.

> "Inspector automatically finds CVEs and network exposure issues in your EC2 instances, Lambda functions, and container images."

## What Inspector Scans

| Target | What It Checks |
|---|---|
| **EC2 Instances** | Installed software packages exposed to CVEs; network reachability (open ports) |
| **Amazon ECR (Container Images)** | Container image vulnerabilities (CVEs) when images are pushed to ECR |
| **AWS Lambda Functions** | Application code vulnerabilities (injection flaws, data leaks, missing encryption) |

## How It Works

### EC2 Scanning

- Uses the **SSM Agent** on EC2 instances to collect software inventory
- Inspector compares installed packages against the **CVE database**
- Also checks **network reachability** — which ports are reachable from the internet

### ECR Scanning

- Triggered automatically when a container image is **pushed to ECR**
- Scans image layers for known CVEs
- Results attached to the image in ECR (visible as image scan findings)

### Lambda Scanning

- Inspects Lambda function code for:
  - Injection vulnerabilities
  - Sensitive data exposure
  - Missing encryption
  - Dependency CVEs

## Findings

Inspector generates **findings** with:
- Severity: **Critical, High, Medium, Low, Informational**
- CVE ID (for package vulnerabilities)
- Affected resource
- Remediation recommendation

Findings are sent to:
- **Amazon EventBridge** — trigger automated remediation
- **AWS Security Hub** — centralized security findings

## Multi-Account Support

- Inspector can be **centrally managed** across multiple accounts using **AWS Organizations**
- A delegated administrator account aggregates findings from all member accounts

## Inspector vs Security Hub vs GuardDuty

| Service | Purpose | What It Analyzes |
|---|---|---|
| **Amazon Inspector** | Vulnerability scanning | Software packages, CVEs, code, network exposure |
| **Amazon GuardDuty** | Threat detection | Behavior, logs, traffic (VPC Flow, CloudTrail, DNS) |
| **AWS Security Hub** | Aggregation | Findings from Inspector, GuardDuty, Macie, etc. |

## Key Points / Exam Tips

- Inspector = **vulnerability scanning** — not threat detection (that's GuardDuty)
- Requires **SSM Agent** for EC2 scanning
- ECR scanning is triggered on **image push** — continuous, not one-time
- Findings go to **EventBridge** and **Security Hub**
- Inspector is **continuous** — it rescans when new CVEs are published, not just on demand
- Supports **EC2, ECR (container images), and Lambda** — know all three targets
- Managed centrally via **AWS Organizations**

## Trigger Words

| Keyword | Think |
|---|---|
| "Find CVEs in EC2/Lambda/containers" | Amazon Inspector |
| "Vulnerability scanning" | Amazon Inspector |
| "Scan container images for vulnerabilities" | Inspector + ECR |
| "Network reachability check on EC2" | Amazon Inspector |
| "Continuously monitor for software vulnerabilities" | Amazon Inspector |
