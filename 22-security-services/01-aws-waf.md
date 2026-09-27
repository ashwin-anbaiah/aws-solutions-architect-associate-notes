# AWS WAF (Web Application Firewall)

## What is AWS WAF?

**AWS WAF** is a **Layer 7 web application firewall** that protects web applications from common web exploits and attacks — including the OWASP Top 10 vulnerabilities.

> "WAF = HTTP/HTTPS traffic inspection and filtering before it reaches your application."

## Where WAF Can Be Deployed

WAF is deployed in front of:

| Resource | Notes |
|---|---|
| **Amazon CloudFront** | Global edge locations — closest to users |
| **Application Load Balancer (ALB)** | Regional — protects apps behind ALB |
| **Amazon API Gateway** | Regional and Edge APIs |
| **AWS AppSync** (GraphQL APIs) | |
| **Amazon Cognito User Pools** | |

> Note: WAF is **not** deployed on EC2 directly — it sits in front of a supported resource.

## Core Concepts

### Web ACL (Web Access Control List)

A **Web ACL** is the top-level container that holds your WAF rules and is associated with a resource (CloudFront, ALB, etc.).

- **Default action**: Allow or Block (applied to requests not matching any rule)
- Rules are evaluated in **priority order** (lowest number = evaluated first)
- Each rule has an action: **Allow**, **Block**, **Count**, or **CAPTCHA/Challenge**

### Rules

Rules define the conditions for matching traffic and the action to take.

| Rule Type | Description |
|---|---|
| **IP Set Match** | Allow or block traffic from specific IP addresses / CIDR ranges |
| **Geo Match** | Allow or block traffic from specific countries/regions |
| **Size Constraint** | Block requests exceeding a size limit (headers, body, URI) |
| **SQL Injection Match** | Detect SQL injection patterns in request |
| **XSS Match** | Detect cross-site scripting patterns |
| **String/Regex Match** | Match request components against patterns |
| **Rate-based Rule** | Count requests from a single IP over a time window; block when exceeded |

### Rule Groups

- A **Rule Group** is a reusable collection of rules
- Can be your own custom rule group or a **Managed Rule Group**

### Managed Rule Groups

Pre-built rule sets maintained by AWS or third parties:

| Group | Content |
|---|---|
| **AWS-AWSManagedRulesCommonRuleSet** | Core rule set — OWASP Top 10 |
| **AWS-AWSManagedRulesSQLiRuleSet** | SQL injection protection |
| **AWS-AWSManagedRulesAmazonIpReputationList** | Known malicious IPs |
| **AWS-AWSManagedRulesBotControlRuleSet** | Bot detection and management |
| AWS Marketplace (third-party) | Imperva, F5, Trend Micro, etc. |

### IP Sets

- Named collections of IP addresses/CIDR blocks
- Referenced by WAF rules for allow/block decisions
- Can be used across multiple Web ACLs

### Rate-Based Rules

- Count requests from a **single IP address** over a **5-minute window**
- Block the IP when requests exceed a defined threshold
- Common use: **DDoS protection at Layer 7**, brute-force login prevention
- Actions: Block or Count (when in monitoring mode)

## WAF Response Codes

- **HTTP 403 (Forbidden)** — returned to clients when a request is **blocked** by WAF
- **CAPTCHA / Challenge** — WAF can present a CAPTCHA to verify a human user (bot protection)

## WAF Logging and Monitoring

- All Web ACL traffic can be logged to:
  - **Amazon S3** (via Kinesis Firehose)
  - **CloudWatch Logs**
  - **Kinesis Data Firehose** → S3/Redshift/OpenSearch
- WAF metrics published to **CloudWatch** (AllowedRequests, BlockedRequests, CountedRequests)

## Key Points / Exam Tips

- WAF operates at **Layer 7 (HTTP/HTTPS)** — not Layer 3/4 (that's Shield)
- WAF protects: **CloudFront, ALB, API Gateway, AppSync, Cognito** — not EC2 directly
- **Web ACL** contains rules; rules have priority numbers (evaluated lowest first)
- **Rate-based rules** = automatic IP blocking when request rate exceeds threshold
- **Managed Rule Groups** = pre-built protection, no custom logic needed
- WAF blocks with **HTTP 403** response
- AWS Shield Standard protects Layer 3/4; **WAF + Shield = comprehensive DDoS defense**

## Trigger Words

| Keyword | Think |
|---|---|
| "Block SQL injection and XSS" | AWS WAF |
| "Block specific countries" | WAF Geo Match rule |
| "Block IP addresses above request rate" | WAF Rate-based rule |
| "OWASP Top 10 protection" | AWS WAF Managed Rules |
| "Layer 7 web attack protection" | AWS WAF |
| "HTTP 403 response on block" | AWS WAF |
