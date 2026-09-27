# API Gateway Authentication and Authorization

## Authentication Options

API Gateway supports three primary authentication and authorization methods:

## 1. IAM Authorization

- Useful when APIs are **internal** or invoked by other **AWS services** (Lambda, Step Functions, ECS tasks)
- API request must include `authorizationType=AWS_IAM`
- Caller must use **SigV4-signed requests** (Signature Version 4)
- API Gateway validates the SigV4 signature against IAM
- Returns **403 Forbidden** if unauthorized

**IAM Policy example (on the caller's role):**
```json
{
  "Effect": "Allow",
  "Action": ["execute-api:Invoke"],
  "Resource": "arn:aws:execute-api:us-east-1:account-id:api-id/*/GET/pets"
}
```

**Best for**: Service-to-service calls within AWS; machine-to-machine APIs

## 2. Amazon Cognito Authorization

- **Amazon Cognito** is a managed identity provider for user sign-up, sign-in, and access control
- Uses **JWT tokens** (JSON Web Tokens) for authentication

**Flow:**
1. User signs in to **Amazon Cognito User Pool** → receives JWT tokens (ID token, Access token)
2. Client sends JWT token to API Gateway endpoint (in `Authorization` header)
3. API Gateway validates the JWT token directly against Cognito
4. Invalid → returns **401 Unauthorized**
5. Valid → extracts claims (email, groups, custom attributes) and forwards request to backend
6. Backend handles the request and responds

**Best for**: Web and mobile apps where end-users need to authenticate

## 3. Lambda Authorizer (Custom Authorizer)

- Use when you need **full flexibility** — custom authentication logic or third-party identity providers
- API Gateway invokes a Lambda function to validate the token/key and authorize the request

**Flow:**
1. User authenticates with a **3rd-party IdP** (e.g., Auth0, Okta, custom) → receives JWT or opaque token
2. Client calls API Gateway, includes token in request header
3. API Gateway invokes the **Lambda Authorizer function**
4. Lambda validates the token (calls external API if needed)
5. If valid → Lambda returns an **IAM policy** (allow/deny) + optional context to API Gateway
6. API Gateway evaluates the policy → if allowed, forwards request to backend; otherwise returns **403 Forbidden**

**Lambda Authorizer types:**
- **Token-based**: Validates a bearer token in the Authorization header
- **Request-based**: Validates based on request parameters (headers, query strings, etc.)

**Best for**: Third-party OAuth/OIDC providers, custom auth logic, fine-grained authorization

## 4. API Gateway Resource Policy

- **Resource Policies** control access at the **API level** — before authentication/authorization
- Attached directly to the API Gateway resource (similar to S3 bucket policies)
- Can allow or deny access based on:
  - AWS accounts
  - IAM principals
  - **Source IP ranges** (CIDR)
  - **VPC Endpoints** (for Private APIs)
  - AWS Organizations

**Common use cases:**
- Restrict a public API to specific IP ranges
- Allow cross-account access
- Restrict Private APIs to specific VPC Interface Endpoints

## Comparison Table

| Method | Who uses it | Token type | Best for |
|---|---|---|---|
| **IAM** | AWS services, EC2, Lambda | SigV4 signature | Internal AWS service-to-service |
| **Cognito** | End-users (web/mobile) | JWT (Cognito tokens) | User-facing apps |
| **Lambda Authorizer** | Any | JWT, opaque, API key | 3rd-party IdP, custom logic |
| **Resource Policy** | Network/account level | N/A | IP restriction, cross-account |

## Key Points / Exam Tips

- **IAM auth** = machine-to-machine within AWS; requires SigV4; returns 403 if unauthorized
- **Cognito** = user authentication; JWT tokens; API Gateway validates the token natively
- **Lambda Authorizer** = most flexible; can call external APIs; returns IAM policy to API Gateway
- **Resource Policy** = network-level control; applied before any auth layer
- Cognito returns **401 Unauthorized** on invalid tokens; IAM and Lambda Authorizer return **403 Forbidden**
- Lambda Authorizer results can be **cached** for a specified TTL to reduce Lambda invocations

## Trigger Words

- "3rd-party identity provider (Auth0, Okta) with API Gateway" → Lambda Authorizer
- "User signs in with Cognito and calls API" → Cognito User Pool + API Gateway JWT validation
- "Service-to-service API calls with IAM" → IAM authorization + SigV4
- "Restrict API access to corporate IP range" → Resource Policy with `aws:SourceIp` condition
- "Restrict Private API to specific VPC" → Resource Policy with VPC endpoint condition
