# IAM Identity Center (formerly AWS Single Sign-On)

## What Is IAM Identity Center?

**AWS IAM Identity Center** (renamed from AWS Single Sign-On / SSO in 2022) is AWS's managed service for **centralized Single Sign-On (SSO)** across all accounts in an AWS Organization and business applications. Users log in once and get access to all their authorized AWS accounts and apps without separate credentials per account.

---

## Key Concepts

| Concept | Description |
|---|---|
| **Identity Source** | Where user identities live (built-in directory, AD, external IdP) |
| **Permission Set** | A collection of IAM policies that defines what a user can do; maps to an IAM role in each target account |
| **Assignment** | Linking a user or group to a permission set + a target account |
| **Portal** | The user-facing SSO URL where users log in and see their assigned accounts/apps |

---

## Supported Identity Sources

| Source | Description |
|---|---|
| **Identity Center Directory** | Built-in user directory; simple, no external dependency |
| **AWS Managed Microsoft AD** | Full AD in AWS with trust relationship; supports directory-aware workloads |
| **AD Connector** | Proxy to on-prem AD; no data stored in AWS; least management overhead |
| **External IdP (SAML 2.0)** | Okta, Azure AD/Entra ID, Ping Identity, OneLogin — often with SCIM for auto-provisioning |

**Rule:** "On-prem AD + multi-account SSO + low overhead" → **AD Connector + IAM Identity Center**. AD Connector proxies to on-prem AD; Identity Center maps AD groups to permission sets across accounts.

---

## How Permission Sets Work

```
IAM Identity Center
    Permission Set: "AdministratorAccess"  →  policies: AdministratorAccess
    Permission Set: "BillingReadOnly"      →  policies: ReadOnlyAccess + billing permissions

Assignment:
    User "Alice" (group: admins) → AdministratorAccess → Account-A, Account-B
    User "Bob"                   → BillingReadOnly     → Account-C

When Alice logs into the SSO portal:
    She sees Account-A and Account-B in her list.
    Clicking Account-A assumes an IAM role with AdministratorAccess in Account-A.
```

- Permission sets are **translated into IAM roles** in each target account automatically.
- **Groups** are the recommended way to assign permission sets at scale (not individual users).
- Users assume these roles via the SSO portal or AWS CLI (`aws sso login`).

---

## ABAC (Attribute-Based Access Control) with Identity Center

- Identity Center supports **ABAC** — access decisions based on user attributes (e.g., department, project, cost center) stored in the identity source.
- Permission sets can reference user attributes as conditions in IAM policies.
- Useful for: dynamically scoping access without creating one permission set per team/project.

---

## External IdP Integration (SAML 2.0)

- **Okta, Azure AD (Entra ID), Ping, OneLogin** can be configured as external identity providers.
- Users authenticate against the external IdP; Identity Center receives the SAML assertion and maps it to a permission set.
- **SCIM (System for Cross-domain Identity Management):** automatic user/group provisioning from the external IdP into Identity Center — no manual user creation.

---

## IAM Identity Center vs IAM Federated Role (STS)

| Dimension | IAM Identity Center | IAM Federated Role (STS directly) |
|---|---|---|
| Scope | Organization-wide, multi-account | Single account at a time |
| Permission sets | Managed centrally; auto-creates IAM roles | Manual IAM role creation and management |
| User experience | SSO portal with account/app list | Custom SAML/OIDC integration per account |
| Recommended for | Multi-account organizations, enterprise SSO | Simple single-account federation scenarios |

---

## Key Points / Exam Tips

- IAM Identity Center = the **control plane for SSO** across an organization. It's managed; you don't build the federation plumbing yourself.
- **Permission sets** → become **IAM roles** in each account automatically. Users assume these roles.
- AD Connector proxies to on-prem AD without storing any user data in AWS — lowest operational overhead for existing AD environments.
- SCIM enables automatic provisioning/deprovisioning when users join/leave — no manual identity management.
- ABAC lets you write fewer permission sets by using user attributes as conditions.
- The SSO portal URL is where users land after authentication.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Centralized SSO across all AWS accounts in an org" | IAM Identity Center |
| "On-prem AD, multi-account SSO, minimal AWS overhead" | AD Connector + IAM Identity Center |
| "Permission set defines what a user can do in an account" | IAM Identity Center permission sets |
| "External IdP (Okta/Azure AD) for AWS access" | IAM Identity Center with SAML 2.0 external IdP |
| "Auto-provision users from IdP into AWS" | SCIM integration with IAM Identity Center |
| "ABAC — access based on user attributes" | IAM Identity Center ABAC |
