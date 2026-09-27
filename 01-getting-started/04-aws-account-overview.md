# AWS Account Overview

## What Is an AWS Account?

An **AWS Account** is the fundamental security and billing boundary in AWS. Everything you create — EC2 instances, S3 buckets, databases, VPCs — lives inside an account. Resources in one account are isolated from resources in another account by default.

Think of it like a physical building: the account is the building, and the rooms inside are services and resources. Other buildings (accounts) need explicit permission to enter.

---

## Account Structure

```
AWS Account
├── Root User (1 per account, tied to the sign-up email)
├── IAM Users (individual people or service accounts)
├── IAM Roles (for services and cross-account access)
│
├── Regions
│   ├── Region 1 (N. Virginia)
│   │   ├── VPC
│   │   ├── EC2 instances
│   │   └── RDS databases
│   └── Region 2 (Mumbai)
│       └── More resources...
│
└── Global Services (not Region-specific)
    ├── IAM
    ├── Route 53
    ├── CloudFront
    └── S3 (bucket names are global, but data is stored in a Region)
```

---

## Root User

The **Root User** is the account owner — created automatically when you sign up for AWS using an email address and password.

### Root User Permissions
- Has **unrestricted access** to everything in the account — no IAM policy can limit it
- Cannot be restricted by IAM policies (unlike IAM users and roles)

### Tasks That REQUIRE Root User
Only use the root user for these specific actions:

- Change account settings (account name, email, root password)
- Close the AWS account
- Restore IAM user permissions when an admin is locked out
- Activate IAM access to billing and cost management console
- Configure an S3 bucket to require MFA for deletion
- Change the AWS Support plan

### Root User Best Practices
- **Do not use root for day-to-day operations** — create an IAM admin user instead
- **Enable MFA** on the root user immediately
- **Store root credentials securely** and restrict access — ideally in a shared password vault
- The root account email should be a group/team alias (not a personal email), so that access isn't lost if one person leaves

---

## IAM Users

**IAM Users** are identities within an account with specific, restricted permissions defined by IAM policies. These are for individual people or applications that need to interact with AWS.

- Each person on the team should have their own IAM user — never share credentials
- IAM users have long-term credentials (username/password for console; access keys for CLI/SDK)

---

## Global vs Regional Services

Understanding this distinction is important for architecture and troubleshooting:

| Global Services (no Region selection) | Regional Services (must pick a Region) |
|---|---|
| IAM (users, roles, policies) | EC2, EBS |
| Route 53 (DNS) | RDS, Aurora |
| CloudFront (CDN) | Lambda |
| S3 (bucket names are global, data is regional) | VPC, Subnets |
| AWS Organizations | ECS, EKS |

---

## Multiple Accounts Strategy

Large organizations often use **multiple AWS accounts** rather than one big account. Benefits:
- **Security isolation** — a breach or misconfiguration in one account doesn't affect others
- **Billing separation** — separate cost tracking per team, project, or environment
- **Blast radius reduction** — production mistakes can't accidentally touch development resources

**AWS Organizations** is the service that lets you manage multiple accounts centrally, apply governance policies (Service Control Policies), and consolidate billing.

---

## Key Points / Exam Tips

- **One root user per account** — tied to the account's sign-up email
- **Root user has unrestricted access** — cannot be restricted by IAM policies
- **Never use root for daily operations** — create IAM users/roles instead
- **IAM is a global service** — users and roles work across all Regions
- **Resources are regional by default** — most things you create are in a specific Region
- **Multiple accounts** are a security and governance best practice, managed via AWS Organizations

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Change billing/account settings" | Root user required |
| "Close the account" | Root user required |
| "Lock out admin, need to recover access" | Root user |
| "One identity for multiple AWS accounts" | AWS Organizations + IAM Identity Center |
| "Separate billing per team/project" | Multiple accounts via AWS Organizations |
| "Global service, no Region needed" | IAM, Route 53, CloudFront |
