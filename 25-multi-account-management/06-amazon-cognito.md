# Amazon Cognito

## What Is Cognito?

**Amazon Cognito** is an identity platform for web and mobile applications. It handles user sign-up, sign-in, and access control for your application's users — so you don't build custom authentication or store user credentials yourself. Cognito has two distinct components that serve very different purposes.

---

## User Pools vs Identity Pools

| | User Pools | Identity Pools |
|---|---|---|
| **Purpose** | Authenticate users; manage user identities | Grant temporary AWS credentials to access AWS resources |
| **Returns** | JWT tokens (ID token, access token, refresh token) | Temporary AWS credentials (Access Key, Secret Key, Session Token) |
| **What it does** | Sign-up, sign-in, MFA, password policies, federation | Exchange tokens for IAM role credentials |
| **Who uses it** | Your application to authenticate users | Your application to let authenticated/guest users call AWS services directly |
| **Typical use** | "Is this user who they claim to be?" | "This authenticated user needs to read from S3" |

> **Critical exam trap:** User Pools = authentication/identity management. Identity Pools = AWS resource authorization. They are separate services that are often chained together.

---

## User Pools in Depth

- Managed user directory for your app — stores usernames, passwords, attributes.
- Supports **social federation**: Login with Google, Facebook, Apple, Amazon.
- Supports **enterprise federation**: SAML 2.0 IdPs (AD FS, Okta, Azure AD).
- Supports **MFA** (TOTP, SMS).
- Provides a **hosted UI** for sign-in/sign-up (no custom UI needed).
- Integrates natively with **ALB** — configure an "authenticate" action on listener rules (no custom code in the app).
- Returns **JWT tokens** that your app validates for each request.

### ALB + Cognito User Pools Pattern
```
User → ALB (authenticate action) → Cognito User Pool login
             ↓ (JWT validated)
         EC2 / ECS target group
```
- Native integration — no Lambda, no code changes in the app.
- **CloudFront has NO native Cognito integration** — requires Lambda@Edge (significant extra work).

---

## Identity Pools in Depth

- Takes an identity token (from User Pool, Google, Facebook, SAML, or even anonymous/guest) and exchanges it for **temporary AWS credentials** via AWS STS.
- The credentials are scoped to an IAM role — you define separate roles for authenticated and unauthenticated (guest) users.
- Supports **unauthenticated (guest) access** — users can call AWS services without logging in, under a limited IAM role.
- **Use case:** mobile app users need to read from an S3 bucket or write to DynamoDB directly from the app — they get temporary credentials, not a long-lived IAM user.

### Typical Cognito Flow (User Pool + Identity Pool together)
```
1. User signs in via Cognito User Pool → receives JWT token
2. App passes JWT to Cognito Identity Pool
3. Identity Pool exchanges JWT for temporary AWS credentials (via STS)
4. App uses credentials to call S3/DynamoDB/etc. directly
```

---

## Cognito vs IAM Identity Center

| | Amazon Cognito | IAM Identity Center |
|---|---|---|
| Users | External app users (customers, end-users) | Internal employees / workforce |
| Scale | Millions of consumer identities | Thousands of employees |
| Purpose | App authentication + AWS resource access for users | SSO across AWS accounts for employees |
| Integration | Mobile/web apps | AWS Console, CLI, and business apps |

---

## Key Points / Exam Tips

- **User Pools = authentication** — verify who the user is; return JWT tokens.
- **Identity Pools = authorization** — give users temporary AWS credentials to access AWS resources.
- They are often used together but serve completely different functions.
- **ALB + Cognito User Pool** = native integration, no code needed. **CloudFront + Cognito** = requires Lambda@Edge.
- Identity Pools support **guest/unauthenticated** access — users get limited AWS credentials without logging in.
- Cognito is for **external/consumer users** — IAM Identity Center is for **employees/workforce**.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Authenticate users for a web/mobile app" | Amazon Cognito User Pools |
| "Login with Google/Facebook/SAML for an app" | Cognito User Pools (social/enterprise federation) |
| "Decouple app authentication from application logic, ALB present" | Cognito User Pools + ALB |
| "Users need temporary AWS credentials to access S3 from mobile app" | Cognito Identity Pools |
| "Guest/unauthenticated users need limited AWS access" | Cognito Identity Pools (unauthenticated identities) |
| "JWT token from User Pool exchanged for AWS credentials" | Cognito Identity Pools |
