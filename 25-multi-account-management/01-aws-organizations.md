# AWS Organizations

## What Is AWS Organizations?

**AWS Organizations** is a global service that lets you centrally manage multiple AWS accounts under a single organization. It provides consolidated billing, account grouping via Organizational Units (OUs), and policy-based governance (SCPs, tag policies, backup policies).

---

## Key Concepts

| Concept | Description |
|---|---|
| **Organization** | The top-level container; has one unique Organization ID |
| **Management Account** | The "payer" account that owns the organization; NOT affected by SCPs |
| **Member Account** | Any account that belongs to the organization |
| **Root** | The top-level parent container for all OUs and accounts |
| **OU (Organizational Unit)** | A logical grouping of accounts (e.g., Project A OU, Development OU) |

- Each member account can belong to **only one organization**.
- OUs support up to **5 levels** of hierarchical nesting.
- Organizations supports two modes: **"Consolidated billing only"** or **"All features"** (cannot roll back from All features once enabled).

---

## Organizational Unit (OU) Hierarchy Example

```
Root
├── Management Account
├── Project A OU
│   ├── Development Account
│   ├── Staging Account
│   └── Production Account
├── Project B OU
│   ├── Development Account
│   └── Production Account
└── Company OU
    ├── Finance Account
    └── HR Account
```

---

## Consolidated Billing

- The **management account** pays the bill for the entire organization.
- Member accounts see their individual cost breakdowns.
- **Combined usage discounts:** volume pricing, Reserved Instance discounts, and Savings Plans are shared across the organization. An RI purchased by one account can benefit another account in the same org.
- **No extra fee** for consolidated billing itself.

---

## AWS Organizations Features (when "All features" enabled)

| Feature | Description |
|---|---|
| **Service Control Policies (SCP)** | Set maximum permission guardrails for accounts |
| **Consolidated Billing** | Single bill, combined usage discounts |
| **Tag Policies** | Standardize tagging across accounts (key names, capitalization, allowed values) |
| **Backup Policies** | Enforce backup configurations across all accounts |
| **AI Services Opt-Out Policies** | Prevent AWS from using account data to improve AI services |
| **aws:PrincipalOrgID** | Condition key in IAM resource policies to restrict access to only principals in your org |

---

## Tag Policies

- Define rules for tag **keys** and **values** (required capitalization, allowed values).
- Enforcement modes:
  - **Enforced** — blocks non-compliant tagging on specified resource types.
  - **Monitoring only** — allows tagging but flags non-compliance for reporting.
- Compliance visible via AWS Resource Groups, Tag Editor, and the Tagging API.
- Tag policies do NOT grant or deny service permissions — they only govern tagging behavior.

---

## aws:PrincipalOrgID Condition Key

- Every organization has a unique **Organization ID** (e.g., `o-ea3x9tl9cz`).
- Use `aws:PrincipalOrgID` in IAM **resource-based policies** (S3 bucket policies, KMS key policies, VPC endpoint policies) to restrict access to only principals belonging to your organization.
- Example: only allow S3 GetObject if the requester is in your org — prevents access from accounts outside your organization.

---

## Key Points / Exam Tips

- Management account is **exempt from SCPs** — SCPs only affect member accounts.
- Once "All features" is enabled, it **cannot be rolled back** to "Consolidated billing only."
- Consolidated billing allows **shared Reserved Instance discounts** and **Savings Plans** across all member accounts.
- OUs can be nested 5 levels deep — policies applied at a parent OU flow down to child OUs and accounts.
- Tag policies require "All features" enabled; they standardize tagging, not permissions.
- `aws:PrincipalOrgID` is a clean way to "allow all accounts in my org" without listing each account ID.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Manage multiple AWS accounts under one organization" | AWS Organizations |
| "Single bill for all accounts, share RI discounts" | Consolidated billing via Organizations |
| "Restrict all accounts to a specific region or service" | SCP applied at Root or OU level |
| "Standardize tag keys and allowed values across accounts" | Tag Policies (Organizations) |
| "Allow only org members to access S3 bucket" | aws:PrincipalOrgID condition in bucket policy |
| "Group accounts by project/environment with policy inheritance" | OUs in AWS Organizations |
