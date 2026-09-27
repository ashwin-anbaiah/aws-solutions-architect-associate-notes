# AWS Certificate Manager (ACM)

## What is ACM?

**AWS Certificate Manager (ACM)** provisions, stores, and renews public and private **SSL/TLS certificates (X.509)** used to secure websites and applications with HTTPS.

- Issues **public certificates** (trusted by browsers) and manages **private certificates** (internal use via AWS Private CA)
- **Public certificates are free** when used with integrated AWS services
- Supports **wildcard certificates** (e.g., `*.example.com`) for subdomain coverage
- **Auto-renews** certificates before expiry — no manual steps needed as long as validation remains intact
- Allows **importing** 3rd-party certificates (Let's Encrypt, DigiCert, OpenSSL) — imported certs do NOT auto-renew

## Public vs Private Certificates

| | Public Certificate | Private Certificate |
|---|---|---|
| **Issued by** | Globally trusted CAs (ACM, DigiCert, Let's Encrypt) | Private CA (AWS Private CA) |
| **Browser trust** | Automatic (pre-installed in trust stores) | Manual (must install CA cert in trust store) |
| **Use case** | Public-facing websites, internet APIs | Internal microservices, IoT, VPN, intranets |
| **Validation** | DNS or Email validation required | No public validation needed |
| **Cost** | Free (with AWS services) | Per CA + per certificate fee |

## Certificate Validation Methods

When requesting a **public certificate** from ACM, you must prove domain ownership:

| Method | How it works | Best for |
|---|---|---|
| **DNS validation** | Add a CNAME record to your domain's DNS | Automated (Route 53 can do this automatically); recommended for auto-renewal |
| **Email validation** | ACM sends email to domain admin contacts | Simple setup but manual process |

**Best practice**: Use **DNS validation** with Route 53 — ACM adds the CNAME automatically, and auto-renewal works without any intervention.

## ACM with AWS Services

ACM integrates natively with these services for SSL/TLS termination:

| Service | ACM Certificate Location |
|---|---|
| **CloudFront** | Must be in **us-east-1** (N. Virginia) — always |
| **ALB / NLB** | Same region as the load balancer |
| **API Gateway (Edge-optimized REST)** | Must be in **us-east-1** |
| **API Gateway (Regional / HTTP API)** | Same region as the API |
| **Elastic Beanstalk** | Same region |
| **App Runner, EKS, Amplify** | Same region |

**Critical rule**: CloudFront and edge-optimized API Gateway ALWAYS require ACM certificates in **us-east-1**.

## Importing Certificates

You can import externally created certificates into ACM:
- Must provide: **Certificate + Private Key + Certificate Chain** (intermediate CA certs)
- Imported certificates can be attached to AWS services just like ACM-issued certs
- **Do NOT auto-renew** — you must re-import before expiry
- ACM sends expiry notifications starting **45 days** before expiry (configurable)

## Certificate Lifecycle Events

ACM automatically publishes certificate lifecycle events to **Amazon EventBridge** (no setup required):
- Certificate expiration approaching (45, 30, 15, 7, 1 day)
- Certificate expired
- Renewal action required
- Renewal succeeded / failed

**Use EventBridge rules** to trigger automated actions (Lambda, SNS alert, ticketing system) to avoid downtime from expired certificates.

## Key Points / Exam Tips

- **Public ACM certificates are free** when used with CloudFront, ALB, API Gateway — no charge per certificate
- **Imported certificates cost money** per exported certificate (if exported for use outside AWS services)
- **DNS validation + Route 53** = fully automated renewal with zero manual effort — best practice
- **CloudFront** requires ACM cert in **us-east-1** regardless of where your origin is — very common exam trap
- **Auto-renewal only works** for ACM-issued certs; imported certs must be manually re-imported
- ACM lifecycle events go to EventBridge → create rules to get notified before expiry

## Trigger Words

- "HTTPS for CloudFront with custom domain" → ACM certificate in us-east-1
- "HTTPS for ALB with custom domain" → ACM certificate in ALB's region
- "SSL certificate renewal automation" → ACM with DNS validation
- "Certificate expiry notification" → ACM EventBridge lifecycle events
- "Third-party certificate (Let's Encrypt) in AWS" → Import into ACM (manual renewal)
