# S3 Additional Features (Static Website, Requester Pays, Pre-Signed URLs, CORS, Access Points)

## Static Website Hosting

S3 can host a **static website** (HTML, CSS, JavaScript, images) directly from a bucket — no servers required.

### Setup Steps

1. Upload static HTML files to an S3 bucket
2. **Disable Block Public Access** for the bucket
3. **Add a bucket policy** allowing `s3:GetObject` for `"Principal": "*"`
4. Enable **Static Website Hosting** in bucket properties, specifying `index.html` as the index document

### Website Endpoint Format

```
http://bucket-name.s3-website-Region.amazonaws.com
# or
http://bucket-name.s3-website.Region.amazonaws.com
```

**Limitation:** S3 static websites serve only over **HTTP**, not HTTPS. For HTTPS, place **Amazon CloudFront** in front of the S3 bucket.

---

## Requester Pays

By default, the **bucket owner** pays all costs (storage, requests, data transfer out). With **Requester Pays** enabled, the **requester** pays for data transfer and request costs (the owner still pays for storage).

| Property | Detail |
|---|---|
| **Who pays storage** | Bucket owner |
| **Who pays requests + DTO** | Requester (when Requester Pays is enabled) |
| **Requester requirement** | Must have an AWS account (anonymous access not allowed) |
| **CLI usage** | `--request-payer requester` flag required |
| **API usage** | `x-amz-request-payer: requester` HTTP header required |

**Use cases:** Public datasets, open-access research data, large shared file repositories where many external users download content.

---

## S3 Pre-Signed URLs

A **Pre-Signed URL** provides temporary access to a private S3 object without requiring AWS credentials.

| Property | Detail |
|---|---|
| **Generated using** | IAM credentials of the creator (console or CLI) |
| **Supports** | GET (download) and PUT (upload) |
| **Expiration** | Configurable — URL works until it expires |
| **Permissions** | URL inherits creator's permissions — if creator can't access object, URL won't work |
| **Permission change** | If IAM principal loses permissions AFTER generating the URL, the URL continues to work until expiry |

### Use Cases

- Share a private invoice PDF with a customer (time-limited link)
- Allow a mobile app to upload profile pictures directly to S3 without sharing AWS credentials
- Distribute a course resource link that expires in 1 hour
- Enable temporary downloads without making the bucket public

---

## CORS (Cross-Origin Resource Sharing)

**CORS** is a browser security mechanism that controls which websites can make requests to your S3 bucket from JavaScript code.

### How it works

1. Browser detects a cross-origin request (e.g., `myapp.com` → `mybucket.s3.amazonaws.com`)
2. Browser sends an HTTP **OPTIONS preflight request** to S3
3. S3 checks its CORS rules and returns the allowed origins and methods
4. If allowed, the browser proceeds with the actual request

### S3 CORS Configuration (XML)

```xml
<CORSConfiguration>
  <CORSRule>
    <AllowedOrigin>https://myapp.com</AllowedOrigin>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>PUT</AllowedMethod>
    <AllowedHeader>*</AllowedHeader>
  </CORSRule>
</CORSConfiguration>
```

Use `*` for `AllowedOrigin` to allow all origins (not recommended for production).

### CORS is Browser-Only

CORS is enforced by browsers. It does NOT apply to:
- AWS CLI
- AWS SDK
- Lambda functions
- Postman / cURL
- Direct API calls

---

## S3 Access Points

**S3 Access Points** simplify access management for shared S3 buckets by creating named endpoints with their own access policies.

### Problem They Solve

A single complex bucket policy managing access for dozens of applications becomes unmanageable. Access Points let each application (or team) have its own named endpoint with its own policy.

### Access Point Properties

| Property | Detail |
|---|---|
| **Per-bucket** | Multiple Access Points per bucket |
| **Unique hostname** | `account-id.s3-accesspoint.region.amazonaws.com` |
| **Own access policy** | Each Access Point has its own IAM-style resource policy |
| **Prefix scoping** | Each Access Point can be restricted to a specific prefix |
| **Network control** | Can be **internet-facing** OR **VPC-only** |

### Access Point ARN Example

```
arn:aws:s3:us-east-1:123456789012:accesspoint/finance
arn:aws:s3:us-east-1:123456789012:accesspoint/sales
arn:aws:s3:us-east-1:123456789012:accesspoint/datascience
```

### VPC-Only Access Points

- Configure an Access Point to be accessible **only from a specific VPC**
- Requires a **VPC Endpoint** (Gateway or Interface) for the VPC to reach the Access Point
- The **VPC Endpoint policy** must explicitly allow both the S3 bucket AND the Access Point ARN

---

## Key Points / Exam Tips

- S3 static websites: HTTP only — use CloudFront for HTTPS
- Pre-signed URLs: permissions are evaluated at **generation time** (creator's permissions); if permissions are revoked after creation, URL still works until expiry
- CORS is enforced by **browsers only** — not by CLI, SDK, or Lambda
- Requester Pays: anonymous users cannot use Requester Pays (must have AWS account)
- Access Points: each has its own policy and hostname — great for multi-team shared bucket scenarios
- VPC-only Access Points require a VPC Endpoint AND the endpoint policy must allow the Access Point ARN

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Host static HTML/JS/CSS on S3 without a server" | S3 Static Website Hosting |
| "Allow HTTPS for static website on S3" | CloudFront in front of S3 (S3 alone = HTTP only) |
| "Temporary access to private S3 object without AWS credentials" | Pre-Signed URL |
| "Browser CORS error when accessing S3" | Configure CORS rules on the S3 bucket |
| "External users pay for S3 download costs" | Requester Pays |
| "Manage access to shared S3 bucket for many teams" | S3 Access Points |
| "S3 access restricted to a specific VPC" | VPC-only S3 Access Point + VPC Endpoint |
