# AWS STS (Security Token Service)

## What Is STS?

**AWS Security Token Service (STS)** is the service that issues **temporary, short-lived security credentials**. Every time an IAM role is assumed — by a service, a user, or an external identity — STS is the engine doing the work behind the scenes.

STS is a global service available at `https://sts.amazonaws.com` with no Region selection needed (though regional endpoints exist for lower latency).

---

## What STS Returns

When a role is successfully assumed, STS returns a **set of temporary credentials**:

1. **Access Key ID** — identifies the temporary session (starts with `ASIA...` for temporary keys, vs `AKIA...` for long-term user keys)
2. **Secret Access Key** — the temporary secret
3. **Session Token** — the proof that this is a temporary session, not a long-term key pair

All three pieces expire together. The default expiration is **1 hour** but can be configured from 15 minutes to 12 hours.

---

## Key STS API Actions

| API Action | What It Does |
|---|---|
| `sts:AssumeRole` | Assume an IAM role — returns temporary credentials for that role |
| `sts:AssumeRoleWithWebIdentity` | Assume a role after authenticating with an external web identity (Google, Facebook, Amazon Cognito) |
| `sts:AssumeRoleWithSAML` | Assume a role after authenticating via SAML 2.0 (e.g., Okta, Active Directory Federation Services) |
| `sts:GetFederationToken` | Get temporary credentials for a federated user |
| `sts:GetSessionToken` | Get temporary credentials for an IAM user (useful for enforcing MFA) |

---

## Common STS Use Cases

### 1. EC2 / Lambda Accessing Other AWS Services

When you attach a role to an EC2 instance or Lambda function, under the hood AWS calls STS to generate temporary credentials, makes them available via the Instance Metadata Service (IMDS) on the instance, and automatically renews them before they expire.

### 2. Cross-Account Access

Account A user wants to do something in Account B:
1. Account A user calls `sts:AssumeRole` on a role defined in Account B
2. STS validates the trust policy of that role
3. STS returns temporary credentials scoped to Account B's role permissions
4. User uses those credentials to access Account B resources

```
Account A (Alice)           Account B
   ↓ sts:AssumeRole ───────▶ Role: CrossAccountReader
   ↑ temporary creds ◀─────── STS
   ↓ use creds ────────────▶ S3 bucket in Account B
```

### 3. Federated Access / SSO

External users (corporate employees with AD credentials, or app users logging in via Google) can exchange their external identity for temporary AWS credentials:

```
User authenticates with Okta/AD ──▶ SAML assertion
                                  ──▶ sts:AssumeRoleWithSAML
                                  ◀── temporary AWS credentials
```

This is the foundation of AWS IAM Identity Center (formerly SSO).

---

## IAM Database Authentication — STS in Action

When you use IAM Database Authentication with RDS:
1. Your application calls an SDK method to generate a database auth token
2. Under the hood, this is an STS-signed token valid for **15 minutes**
3. The app connects to RDS using this token instead of a static password
4. RDS validates the token with IAM — the credentials rotate automatically

This is why IAM DB auth tokens are time-limited: they're fundamentally STS tokens.

---

## Why Temporary Credentials Are Better Than Long-Term Keys

| | Long-Term Access Keys | Temporary STS Credentials |
|---|---|---|
| Expiry | Never (until manually deleted) | Auto-expire (15 min – 12 hours) |
| Rotation | Manual, operational burden | Automatic |
| Leak risk | High — valid forever if leaked | Low — expires quickly |
| Storage | Must be stored on instances/code | Delivered dynamically via IMDS |

---

## Key Points / Exam Tips

- **Every IAM role assumption goes through STS** — STS is the mechanism, roles are the policy
- **Temporary credentials = 3 pieces**: Access Key ID + Secret Access Key + Session Token
- **Default expiration is 1 hour** — configurable from 15 minutes to 12 hours
- **Cross-account access always uses `sts:AssumeRole`** — never share long-term credentials across accounts
- **IAM DB auth tokens are STS tokens** — 15-minute validity, require an IAM role with `rds-db:connect` permission

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Short-lived / temporary credentials" | STS |
| "Cross-account access" | sts:AssumeRole |
| "SSO / federated login" | sts:AssumeRoleWithSAML or AssumeRoleWithWebIdentity |
| "IAM database authentication, rotating DB password" | STS-backed 15-minute tokens |
| "Credentials that auto-expire on EC2" | Instance role via STS + IMDS |
