# Amazon CloudFront Overview

## What is Amazon CloudFront?

**Amazon CloudFront** is a global **Content Delivery Network (CDN)** service that delivers content with low latency through AWS edge locations.

- Operates with **100+ Points of Presence (PoPs)** and **10+ Regional Edge Caches (RECs)** globally
- **Regional Edge Caches (RECs)** act as a mid-tier caching layer between origin and edge locations — they store objects longer than edge PoPs, reducing origin load
- Built-in **DDoS protection** via **AWS Shield Standard** (Layer 3/4) at no extra cost
- Integrates with **AWS WAF** for Layer 7 web attack protection (SQL injection, XSS, bot floods)
- Integrates with **ACM** for SSL/TLS certificates and **Route 53** for custom domain names

## How it Works

1. User requests content (e.g., `cdn.example.com/photo.jpg`)
2. CloudFront routes request to the **nearest edge location**
3. **Cache hit** — served immediately from edge cache (fast, low latency)
4. **Cache miss** — fetches from the **origin** (S3, ALB, API Gateway, etc.), caches the response, then serves it

## CloudFront Origins

| Origin Type | Examples |
|---|---|
| **S3 Bucket** | Static assets, images, videos |
| **Custom HTTP Origins (Public)** | EC2, ALB, API Gateway, Lambda Function URL, any HTTPS endpoint |
| **VPC Origins (Private)** | Private EC2, private ALB, NLB inside a VPC |

## Cache Behavior and TTL

- **TTL (Time To Live)**: how long an object stays cached before CloudFront re-fetches from origin
- **Default TTL**: 24 hours. You can also configure minimum and maximum TTL per cache behavior.
- **Lower TTL** → Fresher content, higher origin load and cost
- **Higher TTL** → Better performance, lower cost, but slower propagation of content updates
- When TTL expires and a new request arrives:
  - Content **unchanged** → origin returns **HTTP 304 Not Modified** → CloudFront resets TTL and serves the cached copy
  - Content **changed** → origin returns **HTTP 200 OK** with new content → CloudFront replaces cached copy and resets TTL

## Cache Invalidation

Use cache invalidation to remove objects from edge caches **before TTL expires**.

- Supports specific paths (`/venice.jpg`), wildcards (`/images/*`), or all objects (`*`)
- **First 1,000 invalidation paths per month are free**; additional paths incur a charge
- Invalidation applies to **all edge locations globally**
- Common use cases: deploying new static asset versions, removing sensitive or incorrect files

## CloudFront Geo Restriction

- **Allowlist**: Only users from approved countries can access your distribution
- **Blocklist**: Users from specified countries receive a **403 Forbidden** response
- CloudFront uses a **3rd-party Geo-IP database** to identify viewer country
- Common use: copyright enforcement, regional licensing restrictions

## Key Points / Exam Tips

- CloudFront is the answer when you see: "reduce global latency", "cache at edge locations", "CDN for S3"
- **Regional Edge Caches** sit between origin and edge PoPs — content cached there longer, further reducing origin hits
- **AWS Shield Standard** is included free with CloudFront; **Shield Advanced** is optional (paid) for enhanced DDoS protection
- For custom domain + HTTPS on CloudFront, the **ACM certificate MUST be created in us-east-1** (N. Virginia), regardless of your origin's region
- Cache invalidation is the right tool when you need to push changes immediately before TTL expires
- Data transfer **from AWS origins to CloudFront is free** — you only pay for data transfer out to viewers

## Trigger Words

- "Reduce latency for global users" → CloudFront
- "Cache content at edge locations" → CloudFront
- "CDN" → CloudFront
- "DDoS protection + web application firewall" → CloudFront + Shield + WAF
- "Serve S3 static content globally with low latency" → CloudFront with S3 origin
- "Serve private/restricted content to authenticated users" → CloudFront Signed URLs/Cookies
