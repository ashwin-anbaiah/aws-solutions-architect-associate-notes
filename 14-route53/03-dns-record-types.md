# DNS Record Types (A, AAAA, CNAME, Alias)

## Core Record Types for the Exam

### A Record
- Maps a **domain name to an IPv4 address**
- Example: `example.com → 11.22.33.44`
- Used to point a domain directly to an EC2 instance's public IP

### AAAA Record
- Maps a **domain name to an IPv6 address**
- Example: `example.com → 2001:db8:abcd:1234::1`

### CNAME Record
- Maps a **hostname to another hostname**
- Example: `www.example.com → example.com`
- **Cannot be used at the zone apex** (e.g., cannot create a CNAME for bare `example.com`)
- DNS query charges apply when CNAME resolves

### Alias Record (Route 53 specific)
- Maps a **hostname to an AWS resource** (CloudFront distribution, ALB, NLB, S3 website endpoint, API Gateway, etc.)
- **AWS Route 53-specific** — not a standard DNS record type
- **Can be used at the zone apex** (e.g., `example.com → alb-dns-name`)
- **Free DNS queries** (no charge for Alias record queries)
- **Auto-detects IP changes** — when the target AWS resource's IP changes, Route 53 updates automatically without waiting for TTL

## CNAME vs Alias Comparison

| Feature | CNAME | Alias |
|---|---|---|
| **What it points to** | Any hostname | AWS resource DNS name |
| **Standard DNS record** | Yes | No (Route 53 specific) |
| **Usable at zone apex** | No (`example.com`) | Yes |
| **DNS query charges** | Yes | No |
| **Auto IP change detection** | No (waits for TTL) | Yes (immediate) |
| **Target** | Any DNS hostname | CloudFront, ALB, NLB, S3 website, API Gateway, Elastic Beanstalk, Global Accelerator |

## When to Use Each

| Scenario | Record Type |
|---|---|
| Point `example.com` (apex) to an ALB | **Alias** |
| Point `www.example.com` to `example.com` | **CNAME** or Alias |
| Point subdomain to EC2 public IP | **A record** |
| Point subdomain to an IPv6 resource | **AAAA record** |
| Point `api.example.com` to API Gateway | **CNAME** or Alias |
| Point `example.com` to CloudFront | **Alias** (only option at apex) |

## Important Notes About Alias Records

- Alias targets supported: **CloudFront distributions, ALB, NLB, API Gateway, S3 website endpoints, Elastic Beanstalk, Global Accelerator, another Route 53 record in the same hosted zone**
- You **cannot** set Alias targets to: **EC2 DNS names** (use A record with the IP instead)
- Alias records support **health checks** on the target
- Cannot create an Alias record pointing to a CNAME record (no chaining)

## Key Points / Exam Tips

- **Zone apex problem**: You cannot use CNAME at `example.com` — only Alias works at zone apex
- **Alias is free** and more resilient than CNAME for AWS resources
- For ALB or CloudFront, always use **Alias** record (not CNAME) when pointing the apex domain
- If the question says "point domain to ALB with no DNS query charges" → Alias record
- CNAME records are valid for subdomains pointing to external (non-AWS) hostnames
- Alias records cannot point to EC2 DNS names or RDS endpoint names — use A records for those

## Trigger Words

- "Point apex domain to ALB / CloudFront" → Alias record
- "No DNS query charges" → Alias record
- "Map subdomain to another domain name" → CNAME
- "Point domain to EC2 public IP" → A record
- "AWS resource changes IP automatically" → Alias record handles this
