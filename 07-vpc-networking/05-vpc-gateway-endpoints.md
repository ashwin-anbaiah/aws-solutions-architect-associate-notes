# VPC Gateway Endpoints (S3 and DynamoDB)

## What is a VPC Gateway Endpoint?

A **VPC Gateway Endpoint** provides a private connection between your VPC and Amazon S3 or Amazon DynamoDB — without requiring an Internet Gateway, NAT Gateway, VPN, or Direct Connect.

Traffic between your VPC and these services stays on the **AWS private network** (never traverses the public internet).

---

## Supported Services

**Gateway Endpoints support ONLY:**
- **Amazon S3**
- **Amazon DynamoDB**

For all other AWS services (SQS, SNS, Kinesis, EC2 API, etc.), use Interface Endpoints (PrivateLink).

---

## How It Works

1. You create a Gateway Endpoint for S3 or DynamoDB in your VPC
2. AWS creates a **Prefix List** (a set of IP ranges for S3 or DynamoDB, formatted as `pl-xxxxxxxx`)
3. You **modify the subnet route table** to add an entry: `pl-xxxxxxxx → vpce-xxxxxxxx`
4. Now traffic destined for S3/DynamoDB from that subnet goes through the endpoint, not via the internet

```
Private Subnet Route Table:
  10.0.0.0/16     → local
  pl-xxxxxxxx     → vpce-xxxxxxxx (S3 Gateway Endpoint)
```

---

## Key Properties

| Property | Detail |
|---|---|
| **Cost** | **Free** — no hourly charge, no data processing charge |
| **Availability** | Horizontally scaled, redundant, highly available |
| **Bandwidth** | No bandwidth constraints |
| **Security** | Can attach Endpoint Policies to restrict which S3 buckets or DynamoDB tables are accessible |
| **Accessibility** | Only accessible from **within the VPC** (same region) |

---

## Cannot Be Accessed From

- On-premises networks (over VPN or Direct Connect) — Gateway Endpoints are **VPC-local only**
- Peered VPCs — a peered VPC cannot use another VPC's Gateway Endpoint
- For cross-VPC or on-premises S3 access over private network, use an **Interface Endpoint** instead

---

## Endpoint Policy

You can attach a resource policy to the Gateway Endpoint to restrict access:

Example — Allow only a specific S3 bucket:
```json
{
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:*",
    "Resource": "arn:aws:s3:::my-bucket/*"
  }]
}
```

You can also use S3 Bucket Policy conditions to enforce access only via a specific endpoint:
- `"aws:sourceVpce": "vpce-1a2b3c4d"` — restrict to specific endpoint
- `"aws:sourceVpc": "vpc-111bbb22"` — restrict to specific VPC

---

## Security Group Consideration

- Gateway Endpoints themselves do not have Security Groups
- If your EC2's Security Group has a **custom outbound rule** (not "allow all"), you must add the S3 prefix list to the outbound rules

---

## Why Use Gateway Endpoints?

| Without Gateway Endpoint | With Gateway Endpoint |
|---|---|
| Private subnet → NAT Gateway → Internet → S3 | Private subnet → Gateway Endpoint → S3 (private network) |
| Costs NAT GW hourly + per-GB data processing fee | **Free** |
| Traffic goes through the internet | Traffic stays on AWS private network |

---

## Key Points / Exam Tips

- Gateway Endpoints are **free** — always recommend them over routing S3/DynamoDB traffic through NAT Gateway
- **Only S3 and DynamoDB** — everything else uses Interface Endpoints
- Gateway Endpoints modify the **route table**, not a Security Group or DNS
- **Cannot be used from on-premises or peered VPCs** — for those use cases, Interface Endpoint for S3 is required
- The prefix list (`pl-xxxxxxxx`) represents the IP ranges for the service — treat it like a CIDR block in route tables and Security Group rules
- Default endpoint policy grants **full access** — restrict with a custom endpoint policy if needed

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Private access to S3 from a VPC (no internet)" | VPC Gateway Endpoint |
| "Reduce NAT Gateway charges for S3 traffic" | VPC Gateway Endpoint (free alternative) |
| "Restrict which S3 bucket a VPC can access" | Endpoint Policy on Gateway Endpoint |
| "On-premises access to S3 over private connection" | Interface Endpoint for S3 (not Gateway Endpoint) |
| "S3 access from peered VPC" | Interface Endpoint for S3 (not Gateway Endpoint) |
| "Free VPC endpoint" | Gateway Endpoint (S3 or DynamoDB only) |
