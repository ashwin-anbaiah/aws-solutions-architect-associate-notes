# CloudFront Origins and Cache Behaviors

## Multiple Origins with Cache Behaviors

CloudFront can route viewer requests to **different origins** based on request attributes. This is done using **Cache Behaviors**, which map URL path patterns to specific origins.

**Example path-based routing:**

| URL Path Pattern | Origin |
|---|---|
| `/api/*` | API Gateway |
| `/auth/*` | Application Load Balancer (ALB) |
| `/images/*` | S3 Bucket |
| `*` (default) | Main web server (EC2 / ALB) |

- Each cache behavior can have its own TTL, caching settings, and viewer protocol policy
- The **most specific path pattern** wins when multiple behaviors match a request

## S3 as a CloudFront Origin

S3 is the most common CloudFront origin for large-scale static media delivery:

- Reduces **data transfer costs** (S3-to-CloudFront transfer is free; first 1 TB/month of DTO is free)
- CloudFront caches objects at edge locations so S3 is queried far less
- **Block direct S3 access** to force all traffic through CloudFront using OAI or OAC

### Origin Access Identity (OAI) — Legacy

- Creates a special IAM-like identity for CloudFront
- S3 Bucket Policy grants `s3:GetObject` to the OAI principal
- Uses **SigV2** for authentication — being phased out

### Origin Access Control (OAC) — Recommended

- Modern replacement for OAI
- Uses **AWS Signature Version 4 (SigV4)** for stronger authentication
- Supports **SSE-KMS encrypted S3 buckets** (OAI does not)
- S3 Bucket Policy grants `s3:GetObject` to `cloudfront.amazonaws.com` with a condition on `AWS:SourceArn` matching the specific distribution ARN
- **Cannot be used** with S3 **static website endpoints** — treat those as a Custom HTTP Origin instead

## Restricting Origin Access from CloudFront Only

For ALB, NLB, and EC2 origins, restrict access at the **network layer** so only CloudFront edge IPs can reach them:

- **AWS Managed Prefix List** (`com.amazonaws.global.cloudfront.origin-facing`) — recommended; automatically maintained by AWS, reference directly in Security Group inbound rules
- **AWS ip-ranges.json** — manual approach; filter by `"service": "CLOUDFRONT"` and `"region": "GLOBAL"`; requires automation (Lambda + EventBridge) to stay current because CloudFront IP ranges change

## Custom HTTP Origins

Any publicly accessible HTTP/HTTPS endpoint can be a CloudFront origin:

- EC2 instance (must have public/EIP)
- ALB (public DNS)
- API Gateway (public endpoint)
- Lambda Function URL
- Any custom HTTP/HTTPS server on-premises or elsewhere

For **private VPC resources**, use **VPC Origins** — CloudFront creates an ENI in your VPC to privately reach private EC2, private ALB, or NLB without making them public.

## Viewer Protocol Policy

Controls how viewers communicate with CloudFront:

- **Allow HTTP and HTTPS** — accept both
- **Redirect HTTP to HTTPS** — forces HTTPS (most common)
- **HTTPS Only** — reject plain HTTP requests

## Origin Protocol Policy

Controls how CloudFront communicates with the origin:

- **HTTPS Only** — secure origin communication
- **Match Viewer** — matches whatever the viewer used
- **HTTP Only** — use only for origins that do not support HTTPS

## Key Points / Exam Tips

- **OAC is the recommended** way to restrict S3 access; OAI is legacy — know both for the exam
- OAC supports **SSE-KMS** encrypted S3 buckets; OAI does not — this is a key differentiator
- S3 **static website endpoints** must be treated as Custom Origins — OAC/OAI do not work there
- Path-based routing uses **Cache Behaviors** — the more specific pattern wins
- To restrict ALB/EC2 access to CloudFront only, use the **AWS Managed Prefix List** (easier than ip-ranges.json)
- VPC Origins allow CloudFront to reach **private** ALB, EC2, or NLB without public exposure

## Trigger Words

- "Allow only CloudFront to access S3" → OAC (recommended) or OAI (legacy)
- "S3 objects encrypted with KMS + CloudFront origin" → must use OAC
- "Route /api/* to API Gateway and /static/* to S3" → CloudFront Cache Behaviors
- "Block direct access to ALB, allow only CloudFront" → Security Group with CloudFront Managed Prefix List
