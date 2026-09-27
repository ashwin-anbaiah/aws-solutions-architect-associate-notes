# AWS Private CA (Certificate Authority)

## What is AWS Private CA?

**AWS Private CA (formerly ACM Private CA)** is a fully managed, highly available **private Certificate Authority** service. It enables you to issue and manage private X.509 certificates for internal use — without managing your own CA hardware (HSMs) or CA servers.

- Creates **private, internal-use certificates** (not trusted by browsers by default)
- Used for: internal microservices, IoT devices, VPN clients, intranet applications, connected cars
- Supports **hierarchical CA structures** (Root CA + Subordinate CAs) to match enterprise PKI design
- Enables **mTLS (mutual TLS)** — issue client certificates for two-way authentication
- Supports certificate revocation via **CRL (Certificate Revocation List)** and **OCSP**

## Private CA vs ACM Public Certificates

| | ACM Public Certificate | AWS Private CA Certificate |
|---|---|---|
| **Browser trust** | Automatic | Not trusted unless CA cert is installed |
| **Internet-facing** | Yes | No (internal only) |
| **Cost** | Free (with AWS services) | Monthly CA fee + per-cert fee |
| **Validation** | DNS or Email domain validation | No public validation required |
| **Certificate type** | Server certificates only | Server + client certificates (mTLS) |
| **Customization** | Limited | Full control (policies, extensions, key types) |
| **Validity period** | Managed by AWS | Fully configurable (short-lived possible) |

## Hierarchical CA Structure

AWS Private CA supports enterprise-grade **CA hierarchies**:

```
Root CA (offline/managed)
    |
    +-- Subordinate CA (AWS Private CA)
            |
            +-- Intermediate CA (optional)
                    |
                    +-- End-entity certificates (servers, clients, IoT devices)
```

- **Root CA** can be hosted in AWS Private CA or as an external offline Root CA
- **Subordinate CAs** are used for day-to-day certificate issuance
- Compromise of a Subordinate CA does not compromise the Root CA

## Key Use Cases

1. **mTLS for internal microservices**: Issue client certificates to each service; services verify each other's identity
2. **IoT device authentication**: Each IoT device gets a unique client certificate for secure gateway communication
3. **VPN client certificates**: Authenticate VPN users with client certificates
4. **Short-lived certificates** (zero-trust): Issue certificates that expire in hours/days — eliminates revocation risk
5. **CloudFront mTLS**: Private CA required for certificate revocation (CRL/OCSP) when using mTLS at CloudFront

## Certificate Revocation

Private CA supports two revocation mechanisms:
- **CRL (Certificate Revocation List)**: Published to an S3 bucket; clients check CRL periodically
- **OCSP (Online Certificate Status Protocol)**: Real-time status check; requires a CA that issues OCSP responses
- CloudFront mTLS **requires AWS Private CA** for revocation support (standard ACM certs cannot be revoked)

## Pricing

- **Per CA per month**: ~$400/month per active CA (priced per CA, not per certificate)
- **Per certificate issued**: $0.75 per certificate
- Unlike public ACM certificates, these are **not free** — cost is a significant differentiator

## Integration with ACM

- ACM natively integrates with AWS Private CA for **private certificate issuance**
- Once issued, private certificates managed through ACM can be attached to ALB, API Gateway, etc.
- Private CA manages the CA infrastructure; ACM manages the certificate lifecycle for AWS services

## Key Points / Exam Tips

- Private CA is the answer when: "internal certificates", "mTLS between microservices", "IoT device certs", "client certificates"
- **Not free** — unlike public ACM certs; expect ~$400/month per CA
- **CloudFront mTLS revocation** requires AWS Private CA — ACM alone is not enough
- Hierarchical CA = **Root CA + Subordinate CAs** — best practice for enterprise PKI
- Private CA certificates are NOT trusted by browsers by default — client systems must trust the private CA's root cert
- Short-lived certificates are a **zero-trust** best practice — avoid long-term credentials

## Trigger Words

- "Internal microservice mTLS authentication" → AWS Private CA
- "IoT device certificate management" → AWS Private CA
- "CloudFront mTLS with certificate revocation (CRL/OCSP)" → AWS Private CA required
- "Enterprise PKI with Root CA and subordinate CAs" → AWS Private CA hierarchy
- "Client certificates for VPN users" → AWS Private CA
