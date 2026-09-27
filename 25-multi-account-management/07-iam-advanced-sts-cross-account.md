# IAM Advanced — STS, Cross-Account Access, and Federated Roles

## AWS Security Token Service (STS)

**AWS STS** is the service that issues **temporary security credentials** when an IAM role is assumed. These credentials are time-limited and automatically expire — unlike long-term IAM user access keys.

### Temporary Credentials Components

| Component | Description |
|---|---|
| **Access Key ID** | Identifies the temporary credential |
| **Secret Access Key** | Used to sign requests |
| **Session Token** | Proves the credential came from STS (required with temp creds) |
| **Expiration** | Default: 1 hour; configurable 15 minutes to 12 hours |

---

## STS API Operations

| API | Use Case |
|---|---|
| **AssumeRole** | AWS-to-AWS: EC2 assuming a role to access S3; cross-account role assumption |
| **AssumeRoleWithSAML** | Enterprise SSO: user authenticates via AD FS, Okta, Azure AD (SAML 2.0) |
| **AssumeRoleWithWebIdentity** | Mobile/web app SSO: user logs in via Google, Facebook, or Cognito User Pool |
| **GetSessionToken** | MFA-enabled access; returns temp creds for existing IAM user with MFA |
| **GetFederationToken** | Federation for custom identity broker scenarios |

---

## IAM Role Types

### Service Role
- An AWS service (EC2, Lambda, ECS) assumes the role to act on your behalf.
- Example: Lambda function writes to S3 and CloudWatch Logs.
- Trust policy: `ec2.amazonaws.com` or `lambda.amazonaws.com` as the trusted principal.

### Service-Linked Role
- A special service role **created and managed by AWS**, not you.
- Pre-defined permissions; you cannot edit the policy.
- Example: Auto Scaling, RDS, Elastic Beanstalk — create SLRs automatically.
- **Exempt from SCPs** — deliberate design to prevent broken AWS service automation.

### Cross-Account Role
- Allows an IAM principal in **Account A** to assume a role in **Account B**.
- Requires a **trust policy** in Account B's role specifying Account A as the trusted principal.
- Requires an IAM policy in Account A allowing `sts:AssumeRole` on Account B's role ARN.

### Federated Role
- Used when external identities (AD, Google, Okta) need temporary AWS access.
- Users authenticate with an IdP, receive a SAML assertion or OIDC token, and exchange it for AWS credentials via STS.

---

## Cross-Account Role Assumption Flow

```
Account A (User: Ben)
    IAM Policy → allows: sts:AssumeRole
                 on: arn:aws:iam::<AccountB>:role/CrossAccountRole

Account B (Role: CrossAccountRole)
    Trust Policy → trusts: arn:aws:iam::<AccountA>:root (or specific user)
    Permission Policy → AmazonS3ReadOnlyAccess

Flow:
1. Ben calls STS AssumeRole with the Account B role ARN
2. STS validates: Does Account A's Ben have permission to assume? Yes (IAM policy)
3. STS validates: Does Account B's role trust Account A? Yes (trust policy)
4. STS returns temporary credentials for CrossAccountRole in Account B
5. Ben uses those credentials to call S3 APIs in Account B
```

---

## ExternalId Condition (Confused Deputy Problem)

### The Confused Deputy Problem
- When a third-party vendor (SaaS) assumes a role in your account on your behalf, any customer of that vendor could potentially trick the vendor into accessing **your** resources if the vendor uses a shared role ARN.
- A malicious customer tells the vendor: "use Account X's role ARN" → vendor blindly assumes it.

### The Fix: ExternalId
- You add an `ExternalId` condition to the trust policy of your cross-account role.
- The vendor must pass this secret `ExternalId` value when calling AssumeRole.
- Since only you and the vendor know the ExternalId, a confused deputy attack fails.

```json
"Condition": {
  "StringEquals": {
    "sts:ExternalId": "unique-secret-per-customer-12345"
  }
}
```

- Best practice: use a **unique ExternalId per customer** — not one shared value for all customers.

---

## IAM Identity Center vs STS Federation (When to Use Which)

| Scenario | Use |
|---|---|
| Workforce SSO across many AWS accounts | **IAM Identity Center** |
| One-time or programmatic cross-account access | **STS AssumeRole** directly |
| Third-party vendor needs role access | **STS AssumeRole + ExternalId** |
| Mobile app users need AWS credentials | **STS AssumeRoleWithWebIdentity** (via Cognito Identity Pools) |
| Enterprise AD/SAML federation (single account) | **STS AssumeRoleWithSAML** (IAM Federated Role) |

---

## Key Points / Exam Tips

- Temporary credentials always include all three: **Access Key ID + Secret Access Key + Session Token** — you cannot use temporary credentials without the session token.
- Cross-account access requires permission on **both sides**: Account A (allow AssumeRole action) AND Account B (trust policy trusting Account A).
- **Service-linked roles** are SCP-exempt — an SCP cannot accidentally break them.
- **ExternalId** solves the confused deputy problem — always use a unique value per third-party integration.
- `AssumeRoleWithWebIdentity` is used for Cognito Identity Pools / Google / Facebook logins.
- `AssumeRoleWithSAML` is used for enterprise AD FS / Okta / Azure AD SSO.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "EC2 instance access to S3 without hardcoded credentials" | IAM service role + instance profile (AssumeRole) |
| "Third-party SaaS vendor needs to access your AWS account" | Cross-account role + ExternalId condition |
| "Cross-account access, temporary credentials" | STS AssumeRole |
| "Mobile app login via Google, get AWS credentials" | STS AssumeRoleWithWebIdentity (Cognito Identity Pool) |
| "Enterprise AD login for AWS access" | STS AssumeRoleWithSAML |
| "Prevent confused deputy attack from SaaS vendor" | ExternalId condition in trust policy |
