# CloudFront Functions and Lambda@Edge

## Serverless Edge Compute Overview

Both CloudFront Functions and Lambda@Edge allow you to **run code at CloudFront edge locations** to modify requests and responses without calling your origin. They are applied at four event hooks:

1. **Viewer Request** — after CloudFront receives request from viewer, before cache lookup
2. **Origin Request** — before CloudFront forwards a cache-miss request to origin
3. **Origin Response** — after CloudFront receives response from origin, before caching
4. **Viewer Response** — before CloudFront returns the response to the viewer

## CloudFront Functions

- Lightweight **JavaScript** functions that run at the edge — designed for **ultra-low latency**
- Massively scalable: handles **tens of millions of requests/second**
- Supports only **Viewer Request** and **Viewer Response** triggers
- Runs **before cache lookup** on Viewer Request

**Capabilities and limitations:**

| Feature | Value |
|---|---|
| Language | JavaScript only |
| Execution time | Less than 1 ms |
| Memory | Max 2 MB |
| Package size | Up to 10 KB |
| External network calls | Not supported |
| File system access | Not supported |
| Environment variables | Not supported |
| Modify response body | Not supported (headers/URI/status only) |

**Common use cases:**
- URL rewrites and redirects (e.g., rewrite SPA routes to `/index.html`)
- HTTP header manipulation (add/remove/modify request or response headers)
- Simple auth checks (validate JWT claims from header without external calls)
- A/B testing with simple random logic

## Lambda@Edge

- Full **Lambda functions** deployed to CloudFront edge locations globally
- Supports **all four event triggers** (Viewer Request, Origin Request, Origin Response, Viewer Response)
- Functions must be **authored in us-east-1** (N. Virginia) — CloudFront replicates them globally

**Capabilities:**

| Feature | Value |
|---|---|
| Languages | Node.js and Python |
| Execution time | Typically 10–50 ms (can be more) |
| Memory | 128 MB – 10 GB |
| Package size | Up to 50 MB |
| External network calls | Supported |
| Ephemeral storage (`/tmp`) | 512 MB |
| Environment variables | Supported |

**Common use cases:**
- Authentication and authorization (call external OAuth/OIDC provider)
- A/B testing, device-based routing, or geo-based routing
- Generate custom responses without calling origin (e.g., "site under maintenance" page)
- Image resizing/format conversion on Origin Request
- Rewriting HTML content or injecting custom headers

## Comparison Table

| Feature | CloudFront Functions | Lambda@Edge |
|---|---|---|
| **Trigger Events** | Viewer Request, Viewer Response | All four event types |
| **Execution Time** | < 1 ms | 10–50+ ms |
| **Memory** | Max 2 MB | 128 MB – 10 GB |
| **Package Size** | 10 KB | Up to 50 MB |
| **Runtime** | JavaScript only | Node.js and Python |
| **External API calls** | No | Yes |
| **File system** | No | Yes (/tmp 512 MB) |
| **Performance** | Ultra-low latency | Moderate latency |
| **Cost** | Very low | Higher |
| **Best for** | Lightweight header edits, URL rewrites, simple auth | Complex logic, auth with external APIs, image processing |

## Key Points / Exam Tips

- **Lambda@Edge** supports all 4 triggers; **CloudFront Functions** supports only Viewer Request and Viewer Response
- **CloudFront Functions** cannot make external network calls — if the scenario requires calling an external auth API, use Lambda@Edge
- Lambda@Edge functions must be authored in **us-east-1** regardless of where they execute
- **CloudFront Functions** are significantly cheaper and faster for simple logic
- Neither CloudFront Functions nor Lambda@Edge can modify the **response body** at the Viewer Response trigger — use Lambda@Edge at Origin Request/Response for body modification
- Use **CloudFront Functions** for: URL rewrite, redirect, header manipulation, simple token validation
- Use **Lambda@Edge** for: OAuth/OIDC auth, image resizing, A/B test with complex logic, custom error pages

## Trigger Words

- "Run code at CloudFront edge with sub-millisecond execution" → CloudFront Functions
- "Rewrite SPA routes to index.html at the edge" → CloudFront Functions (Viewer Request)
- "Authenticate users via external OAuth provider at the edge" → Lambda@Edge
- "Resize images dynamically at CloudFront" → Lambda@Edge (Origin Request)
- "Inspect request headers and route to different origins" → Lambda@Edge
