# Application Load Balancer (ALB)

## What is ALB?

The **Application Load Balancer** operates at **OSI Layer 7** (the Application layer). It inspects HTTP/HTTPS request content and makes intelligent routing decisions based on the content of the request — not just the port or IP.

---

## Core Features

- **Protocols**: HTTP, HTTPS, WebSocket, HTTP/2, gRPC
- **Targets**: EC2 instances, ECS containers, IP addresses, Lambda functions
- Supports **multiple listeners** on different ports (e.g., 80, 8080, 443)
- Supports **Target Group Weighting** — useful for blue/green and A/B testing
- Supports **authentication** via Amazon Cognito, OIDC, SAML, Active Directory federation

---

## Content-Based Routing Rules

ALB routes to different Target Groups based on request attributes:

| Routing Type | Example |
|---|---|
| **Host-based** | `api.example.com` → TG1, `www.example.com` → TG2 |
| **Path-based** | `/images/*` → TG1, `/api/*` → TG2 |
| **HTTP method** | GET → TG1, POST → TG2 |
| **Query string** | `?region=asia` → TG1, `?region=europe` → TG2 |
| **Source IP CIDR** | Corporate IP range → TG1, everything else → TG2 |
| **HTTP header value** | Custom header → specific target group |

---

## Advanced Features

| Feature | Detail |
|---|---|
| **HTTP to HTTPS redirect** | Enforce HTTPS by redirecting HTTP requests automatically |
| **Fixed / custom responses** | Return a static 200 or 404 without forwarding to a backend |
| **Sticky sessions (duration-based)** | Cookie name: `AWSALB` — LB sets the cookie |
| **Sticky sessions (application-based)** | Cookie name: `AWSALBAPP` — app sets the cookie |
| **Connection Draining** | Default 300 seconds — lets in-flight requests finish before deregistering a target |
| **Dual Stack DNS** | Supports both IPv4 and IPv6 clients |
| **Dynamic port mapping** | Supports ECS containers that use dynamic host ports |
| **Mutual TLS (mTLS)** | Both client AND server present X.509 certificates for authentication |
| **Client IP preservation** | Real client IP in `X-Forwarded-For` header |
| **Request Tracing** | Unique request ID in `X-Amzn-Trace-Id` header |

---

## ALB Request Flow

```
Client Request
    |
    v
ALB Listener (e.g., port 443)
    |
    v
Listener Rules (evaluated in priority order)
    |-- Rule 1: path=/asia/* --> Target Group 1
    |-- Rule 2: host=api.example.com --> Target Group 2
    |-- Default Rule --> Target Group 3
    |
    v
Target Group (load balancing: round-robin or least outstanding requests)
    |
    v
EC2 / Container / Lambda target
```

---

## Cross-Zone Load Balancing

- **Enabled by default** for ALB (no extra charge)
- Each ALB node distributes traffic to ALL targets across ALL AZs — prevents imbalanced load when AZs have different instance counts

---

## Server Name Indication (SNI)

- ALB can serve **multiple SSL certificates** — one per domain/hostname
- The client specifies the desired hostname during the TLS handshake
- The ALB selects and returns the correct certificate for that domain
- One ALB can serve `www.app1.com`, `www.app2.com`, `www.app3.com` — each with its own SSL cert
- SNI works on both **ALB and NLB** (not CLB)

---

## Key Points / Exam Tips

- ALB does NOT preserve the original client IP at the TCP level — use **X-Forwarded-For** header to get the real client IP in your application
- ALB does **not** have a static IP address — it uses a DNS hostname; if a static IP is required, place an NLB in front of the ALB
- **Cross-zone load balancing is ON by default** for ALB (unlike NLB/GWLB where it is OFF by default)
- Blue/green deployments use **Weighted Target Groups** (e.g., 90% traffic to blue, 10% to green)
- ALB supports Lambda as a target — useful for routing HTTP traffic to serverless functions
- Sticky sessions depend on **cookies** set by the ALB or the application; if the cookie is lost, the client may land on a different instance

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Route based on URL path or hostname" | ALB |
| "Content-based routing" | ALB |
| "Authenticate users via Cognito / SAML / AD" | ALB |
| "HTTP to HTTPS redirect" | ALB |
| "Real client IP at the application layer" | ALB with X-Forwarded-For header |
| "gRPC support on a load balancer" | ALB |
| "A/B testing / canary / blue-green with % traffic split" | ALB with weighted target groups |
| "Multiple SSL certs on one load balancer" | SNI — works on ALB and NLB |
| "Sticky sessions / session affinity" | ALB (AWSALB or AWSALBAPP cookie) |
