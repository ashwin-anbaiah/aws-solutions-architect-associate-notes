# API Gateway Features (Stages, VPC Link, Caching, Throttling)

## API Stages and Versioning

### API Versioning
- Publish multiple versions of your API (e.g., `v1`, `v2`) so clients can upgrade safely
- Different versions can coexist — existing clients using `v1` are unaffected by `v2` changes

### API Stages
- Each deployment creates a **stage** (e.g., `dev`, `test`, `prod`)
- Each stage has independent settings: logging level, throttling, caching, stage variables
- **Stage variables** act like environment variables — pass config to Lambda aliases or HTTP endpoints
- Example: `dev` stage points to Lambda `dev` alias; `prod` stage points to Lambda `prod` alias

## API Keys and Usage Plans

- **API Keys** identify individual clients or applications calling your API
- **Not designed for security/authentication** — use IAM or Cognito for that
- Paired with **Usage Plans** to:
  - Set **request throttling** limits per API key (per second, per month burst)
  - Set **quotas** (max requests per day/week/month per client)
- Useful for monetizing APIs, rate-limiting per partner/client

## API Caching

- Caches API responses at the **stage level** to reduce backend load and improve response times
- **Default cache TTL**: 300 seconds (configurable: 0 sec to 3600 sec)
- Cache size: 0.5 GB to 237 GB (configurable)
- Can be enabled/disabled per stage or per individual method
- Clients can **invalidate the cache** by sending `Cache-Control: max-age=0` header (requires IAM permission)

## Throttling

API Gateway applies throttling at multiple levels:

| Level | Type | Default Limit |
|---|---|---|
| **Account** | Burst limit | 5,000 requests (burst) |
| **Account** | Steady-state | 10,000 requests/second |
| **Stage / Method** | Per-method throttle | Configured per stage |
| **Usage Plan** | Per API key | Configured per usage plan |

- When throttled, API Gateway returns **HTTP 429 Too Many Requests**
- Lambda invoked by API Gateway receives the throttle error (TooManyRequestsException)

## VPC Link — Connecting to Private Backends

API Gateway is a **public service** — it cannot directly access private VPC resources.

### VPC Link V2 (Recommended)
- Connects API Gateway to **ALB** inside your VPC
- Backend targets can be: EC2 instances, ECS tasks, or on-premises servers (via ALB)
- Use case: expose a private internal service via a public or private API Gateway endpoint

### VPC Link V1 (Legacy)
- Connects API Gateway to **NLB** inside your VPC
- Still works but VPC Link V2 with ALB is preferred for new architectures

## Request and Response Transformation

- Use **Mapping Templates** (Velocity Template Language / VTL) to transform:
  - Request payloads before sending to backend (e.g., JSON → XML, rename fields)
  - Response payloads before returning to client (e.g., filter fields, rename)
- Useful when backend expects a different format than what the client sends

## AWS WAF Integration

- Attach **AWS WAF** to API Gateway (regional REST APIs and HTTP APIs) for Layer 7 protection
- Protects against: SQL injection, XSS, bad bots, abnormal request patterns
- WAF is evaluated before the request reaches your backend

## Key Points / Exam Tips

- **Stage variables** are the recommended way to pass environment-specific config (Lambda alias, DB endpoint, etc.)
- API Gateway throttling returns **HTTP 429** — not 503
- **API Caching** is configured at the stage level, not globally across all stages
- **VPC Link V2** = connects to ALB (new); **VPC Link V1** = connects to NLB (legacy)
- Enabling API caching on a stage costs money — it creates dedicated cache infrastructure
- Request/Response **Mapping Templates** (VTL) are used for data transformation between client and backend formats

## Trigger Words

- "Separate dev, test, and prod API deployments" → API Stages
- "Limit specific API client to 1000 requests/day" → API Keys + Usage Plan
- "Reduce DynamoDB calls by caching API responses" → API Gateway stage caching
- "Connect API Gateway to private ALB inside VPC" → VPC Link V2
- "API throttling / too many requests" → HTTP 429, throttle limits on stage or usage plan
- "Transform JSON to XML before sending to legacy backend" → Mapping Template
