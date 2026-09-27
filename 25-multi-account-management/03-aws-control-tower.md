# AWS Control Tower

## What Is AWS Control Tower?

**AWS Control Tower** automates the setup and governance of a secure, multi-account AWS environment — called a **Landing Zone** — based on AWS best practices. Instead of manually configuring Organizations, SCPs, CloudTrail, Config rules, IAM Identity Center, and VPCs for every new account, Control Tower does it in under an hour with a consistent, repeatable framework.

---

## Landing Zone

A **Landing Zone** is the well-architected, multi-account environment provisioned by Control Tower. It includes:

- A pre-configured OU structure (Management, Audit, Log Archive accounts by default)
- Baseline guardrails applied automatically
- Centralized logging (CloudTrail logs sent to a dedicated Log Archive account)
- Centralized security alerts (Audit account)
- SSO enabled via IAM Identity Center

---

## Guardrails

Guardrails are pre-packaged governance rules that Control Tower applies across accounts.

| Type | Mechanism | Example |
|---|---|---|
| **Preventive** | SCPs — block non-compliant actions before they happen | Disallow root access, prevent disabling CloudTrail, enforce MFA |
| **Detective** | AWS Config rules — detect and flag non-compliance after the fact | Detect unencrypted EBS volumes, detect public S3 buckets |

### Guardrail Categories

| Category | Activation |
|---|---|
| **Mandatory** | Always enforced; cannot be disabled | Disallow root access, disallow changes to encryption, disallow public S3 |
| **Elective** | Optional; enable as needed | Disallow SSH/RDP from internet, enforce tagging, disallow RDS snapshot sharing |

---

## Account Factory

**Account Factory** is a template-based provisioning system that creates new AWS accounts pre-configured with:
- Standard VPC configuration (subnets, routing, security groups)
- CloudTrail trail enabled
- Required guardrails applied
- IAM Identity Center access configured
- Tags applied per organization standards

- Account Factory uses **AWS Service Catalog** under the hood to provision accounts.
- **Customizations for Control Tower (CfCT)** — an extension pattern that allows injecting custom CloudFormation into Account Factory workflows for org-specific customizations.

---

## Control Tower Dashboard

- Centralized view of the entire Landing Zone.
- Shows compliance status of all accounts against guardrails.
- Highlights non-compliant resources organized by account and OU.
- Accessible from the Control Tower console.

---

## How Control Tower Uses Other Services

| AWS Service | Role in Control Tower |
|---|---|
| **AWS Organizations** | Provides the OU structure and account hierarchy |
| **Service Control Policies (SCP)** | Implements preventive guardrails |
| **AWS Config** | Implements detective guardrails; evaluates compliance rules |
| **AWS CloudTrail** | Centralized audit trail across all accounts |
| **IAM Identity Center** | SSO for all Control Tower accounts |
| **AWS CloudFormation** | Account Factory uses it to provision resources |
| **AWS Service Catalog** | Wraps Account Factory as a self-service product |

---

## Key Points / Exam Tips

- Control Tower = **automated setup** of a secure, compliant, multi-account landing zone. You don't have to manually configure all the pieces.
- **Preventive guardrails = SCPs** (block non-compliant actions). **Detective guardrails = Config rules** (detect violations).
- **Account Factory** is the mechanism to provision new accounts consistently with pre-applied guardrails.
- Control Tower is built on top of Organizations, SCP, Config, CloudTrail, IAM Identity Center — it orchestrates all of them.
- "In less than an hour" is the marketing claim — exam may ask about what Control Tower automates that you'd otherwise do manually.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Automated setup of multi-account landing zone with best practices" | AWS Control Tower |
| "Ensure every new account is pre-configured with security guardrails" | Control Tower Account Factory |
| "Guardrail that blocks non-compliant actions before they happen" | Preventive guardrail (SCP) |
| "Guardrail that detects violations after the fact" | Detective guardrail (Config rule) |
| "Template for new account provisioning with standard VPC and trails" | Account Factory |
| "Centralized compliance dashboard for all accounts" | Control Tower Dashboard |
