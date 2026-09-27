# AWS Firewall Manager

## What is AWS Firewall Manager?

**AWS Firewall Manager** is a security management service that lets you **centrally configure and enforce firewall rules across multiple AWS accounts** in an AWS Organization.

> "Firewall Manager = one place to manage WAF, Shield, and Network Firewall across your entire organization."

## What Firewall Manager Manages

Firewall Manager provides centralized management for:

| Service | What It Manages |
|---|---|
| **AWS WAF** | Web ACL rules across ALBs, CloudFront, API Gateways |
| **AWS Shield Advanced** | Enrollment and protection across accounts |
| **Amazon VPC Security Groups** | Audit and enforce SG rules |
| **AWS Network Firewall** | Deploy and manage firewall policies across VPCs |
| **Amazon Route 53 Resolver DNS Firewall** | DNS query filtering rules |

## How It Works

1. **Administrator account** configures Firewall Manager policies in the AWS Organization management account (or a delegated admin)
2. Policies define **which accounts/OUs** are in scope and **what rules** apply
3. Firewall Manager **automatically applies** the policies to new and existing accounts
4. New accounts added to the Organization are **automatically protected**

```
AWS Organization (Management Account)
        ↓
Firewall Manager Administrator Account
        ↓
Firewall Manager Policy (e.g., WAF rules for all ALBs)
        ↓
Auto-applied to:  Account A, Account B, Account C, Account D, ...
```

## Key Features

- **Centralized monitoring** — view compliance across all accounts in a single dashboard
- **Auto-protection for new accounts** — when a new account joins the org, it's automatically covered
- **Policy-based enforcement** — policies can be mandatory (enforced) or advisory (audit only)
- **Cross-account Security Hub integration** — Firewall Manager sends findings to Security Hub

## Prerequisites

- AWS **Organizations** must be enabled
- All member accounts must have **AWS Config** enabled (Firewall Manager uses Config to check compliance)
- A Firewall Manager **administrator account** must be designated

## Firewall Manager vs Manual Configuration

| Aspect | Manual (per account) | Firewall Manager |
|---|---|---|
| New account protection | Manual — must configure each time | **Automatic** |
| Rule consistency | Risk of drift per account | **Centrally enforced** |
| Visibility | Per-account console access | **Unified dashboard** |
| Scale | Time-consuming for 100s of accounts | **Scales via Organizations** |

## Key Points / Exam Tips

- Firewall Manager = **centralized management** across an Organization — not a standalone security tool
- Requires **AWS Organizations** + **AWS Config** enabled in member accounts
- New accounts in the Organization are **automatically protected** — this is a key exam point
- Manages: WAF, Shield Advanced, Network Firewall, Security Groups, DNS Firewall
- Sends compliance findings to **Security Hub**
- The "administrator account" is separate from the Organizations management account (can be delegated)

## Trigger Words

| Keyword | Think |
|---|---|
| "Enforce WAF rules across all accounts" | AWS Firewall Manager |
| "New accounts automatically protected" | Firewall Manager + Organizations |
| "Centrally manage Shield Advanced" | Firewall Manager |
| "Single pane for firewall governance" | Firewall Manager |
| "Enforce security group rules org-wide" | Firewall Manager SG policies |
