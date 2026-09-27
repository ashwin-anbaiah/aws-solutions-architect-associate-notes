# EC2 Instance Metadata Service (IMDSv2)

## What Is the Instance Metadata Service?

The **Instance Metadata Service (IMDS)** is a special HTTP endpoint available from within every EC2 instance that provides information about the instance itself. Applications and scripts running on the instance can query this endpoint to discover their own configuration — instance ID, AMI ID, IP addresses, IAM role credentials, and more.

**Magic IP address**: `169.254.169.254` (IPv4) or `[fd00:ec2::254]` (IPv6)  
This is a link-local address — only reachable from within the instance itself, not from outside.

---

## What Metadata Is Available

Querying `http://169.254.169.254/latest/meta-data/` returns a list of available metadata categories:

| Category | What You Get |
|---|---|
| `instance-id` | The EC2 instance ID (e.g., `i-0abc123def456789`) |
| `ami-id` | The AMI used to launch the instance |
| `instance-type` | The instance type (e.g., `t3.medium`) |
| `local-ipv4` | The instance's private IP address |
| `public-ipv4` | The instance's public IP address |
| `public-hostname` | The public DNS hostname |
| `security-groups` | Names of the attached security groups |
| `iam/security-credentials/<role-name>` | Temporary IAM role credentials (Access Key, Secret Key, Token) |
| `placement/availability-zone` | Which AZ the instance is in |
| `user-data` | The user data script passed to the instance (read from here) |

---

## IMDSv1 vs IMDSv2

There are two versions of the metadata service:

| | IMDSv1 | IMDSv2 |
|---|---|---|
| Authentication | None — just an HTTP GET to the IP | Session-oriented — requires a PUT to get a session token first |
| Security | Vulnerable to SSRF attacks | Protected against SSRF (token-based session required) |
| Recommended | No | **Yes — AWS strongly recommends IMDSv2** |

### IMDSv1 Security Risk: SSRF

**Server-Side Request Forgery (SSRF)**: An attacker tricks a web application into making requests to internal addresses on behalf of the server. If a vulnerable app is on an EC2 instance, an attacker could craft a request that causes the app to fetch `http://169.254.169.254/latest/meta-data/iam/security-credentials/` — leaking the IAM role's temporary credentials to the attacker.

IMDSv2 requires a session token obtained via a `PUT` request (which SSRF typically can't do — HTTP redirects from GET to PUT are blocked by default), preventing this attack pattern.

---

## Using IMDSv2 — The Token Flow

Step 1: Get a session token (TTL in seconds — here, 300 seconds = 5 minutes)
```bash
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 300")
```

Step 2: Use the token in subsequent metadata requests
```bash
curl "http://169.254.169.254/latest/meta-data/instance-id" \
  -H "X-aws-ec2-metadata-token: $TOKEN"
```

AWS SDKs (Boto3, Java SDK, etc.) handle this automatically when they need IAM credentials from the instance role — you don't need to write this code manually in applications.

---

## Instance Metadata vs User Data vs Instance Parameters

| | Instance Metadata | User Data | EC2 Parameters |
|---|---|---|---|
| Purpose | Query instance's own information at runtime | Bootstrap script at launch | Launch configuration |
| Can run scripts? | No | Yes | No |
| Dynamic (updates live)? | Some fields yes (e.g., role credentials refresh automatically) | No (fixed at launch) | No |
| Accessed from | Inside the instance (link-local IP) | Inside the instance (same IP, different path) | At launch time (console/CLI) |
| Example | "What is my instance ID?" | "Install Apache on first boot" | "Which subnet to launch in?" |

**Common exam trap**: "Instance metadata" and "run custom scripts" should never be paired. Metadata is read-only information about the instance — it cannot execute anything. User Data is for execution.

---

## IAM Role Credentials via IMDS

When an EC2 instance has an IAM role attached:
- AWS automatically puts temporary credentials in the metadata at: `iam/security-credentials/<role-name>`
- Credentials include: `AccessKeyId`, `SecretAccessKey`, `Token`, and `Expiration`
- AWS automatically rotates these credentials before they expire (~1 hour default)
- The AWS SDK fetches these automatically — no hardcoded credentials needed in your application

This is the secure way EC2 apps get AWS credentials without storing them anywhere.

---

## Enforcing IMDSv2

You can enforce IMDSv2-only (disable IMDSv1) at:
- **Instance level**: when launching a new instance or modifying an existing one
- **Account level**: using an IAM policy condition `"ec2:MetadataHttpTokens": "required"`
- **AWS Organizations SCP**: enforce IMDSv2 across all accounts

---

## Key Points / Exam Tips

- **IMDS IP: `169.254.169.254`** — memorize this; it comes up on the exam
- **IMDSv2 is the current best practice** — requires a session token, resistant to SSRF
- **IMDS = information about the instance** — cannot run scripts, cannot trigger actions
- **IAM role credentials are available via IMDS** — AWS SDKs use this automatically
- **User Data is also readable via IMDS** — accessible at the `/latest/user-data` path

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Instance discover its own instance ID, IP, or AZ" | IMDS at 169.254.169.254 |
| "SSRF attack to steal EC2 credentials" | Mitigated by IMDSv2 (token-based) |
| "IAM role credentials on EC2 auto-rotate" | IMDS delivers them; SDK fetches automatically |
| "Run custom scripts from instance metadata" | NOT possible — metadata is read-only info |
| "IP address 169.254.169.254" | Instance Metadata Service (IMDS) |
