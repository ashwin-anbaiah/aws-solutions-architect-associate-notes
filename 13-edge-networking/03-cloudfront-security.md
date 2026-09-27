# CloudFront Security Features

## HTTPS / TLS

- CloudFront supports HTTPS between **Viewer ↔ CloudFront** and **CloudFront ↔ Origin** independently
- You can enforce minimum TLS versions (e.g., TLS 1.2) using **Security Policies**
- For custom domain names (e.g., `example.com`), an **ACM SSL/TLS certificate must be in us-east-1** (N. Virginia)

**Viewer Protocol Policy** options:
- Allow HTTP and HTTPS
- Redirect HTTP to HTTPS (most common)
- HTTPS Only

**Origin Protocol Policy** options:
- HTTPS Only
- Match Viewer
- HTTP Only

## Mutual TLS (mTLS)

- **mTLS** = both the client and CloudFront present certificates during TLS handshake
- The viewer presents a **client certificate** which CloudFront validates against a trusted CA stored in ACM
- CloudFront checks: certificate chain, validity period, and revocation (CRL/OCSP via **AWS Private CA**)
- Works only with **custom domains** — not with the default `*.cloudfront.net` domain
- If the client certificate fails verification, CloudFront returns **403 Forbidden**

## Field-Level Encryption (FLE)

- Adds an extra security layer on top of HTTPS by **encrypting specific sensitive fields** at the CloudFront edge (e.g., credit card numbers, PII)
- Encryption happens at the edge using **asymmetric encryption** — CloudFront uses your **public key**; only the origin application can decrypt using the corresponding **private key**
- Protected fields stay encrypted through proxies, logs, and intermediate systems
- Ensures **only the origin application** can read sensitive values

## Signed URLs and Signed Cookies

Use these to serve **private/restricted content** to authorized users only:

| | Signed URL | Signed Cookie |
|---|---|---|
| **Use case** | Single file or resource | Multiple files / entire section |
| **How** | Expiring URL with signature | Browser cookie with signature |
| **Example** | Shareable download link | Paid video streaming subscription |

**How it works:**
1. Signer has a public/private key pair; private key signs the URL or cookie
2. CloudFront uses the public key to verify the signature and policy (expiration, allowed resource)
3. Valid → serves content; Invalid → **403 Forbidden**

Use **Trusted Key Groups** (preferred) or CloudFront Key Pairs (legacy) as the signing authority.

## AWS WAF Integration

- Attach **AWS WAF** to CloudFront to protect against Layer 7 threats: SQL injection, XSS, bad bots, request floods
- WAF filtering happens **before traffic reaches your origin**, reducing backend load
- WAF Web ACLs are associated with CloudFront at the distribution level
- Works globally across all edge locations

## AWS Shield Integration

| | Shield Standard | Shield Advanced |
|---|---|---|
| **Cost** | Free (automatic) | Paid |
| **Protection** | Layer 3/4 DDoS | Enhanced L3/4 + near real-time visibility |
| **Support** | None | 24/7 AWS DDoS Response Team (DRT) |
| **Coverage** | CloudFront, Route 53 | CloudFront, ALB, EC2, Global Accelerator |

## Key Points / Exam Tips

- **ACM certificate for CloudFront custom domains MUST be in us-east-1** — this is a very common exam question
- **mTLS** requires **AWS Private CA** for certificate revocation (CRL/OCSP) — standard ACM certs alone are not enough
- **FLE** uses asymmetric encryption at the edge — only the origin can decrypt specific sensitive fields
- **Signed URLs** = single file access; **Signed Cookies** = multiple files or entire section (e.g., streaming)
- CloudFront is a valid **AWS WAF attachment point** alongside ALB, API Gateway, and App Runner
- **Shield Standard** is automatic and free with CloudFront; you do not need to enable it manually

## Trigger Words

- "Encrypt sensitive fields like credit card numbers at the edge" → CloudFront Field-Level Encryption
- "Restrict content to paid subscribers / authenticated users" → CloudFront Signed URLs or Signed Cookies
- "Client certificate authentication at CloudFront" → mTLS
- "Protect CloudFront from SQL injection and XSS" → AWS WAF attached to CloudFront
- "mTLS revocation / CRL / OCSP at CloudFront" → requires AWS Private CA
