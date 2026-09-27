# SSM Session Manager

## What is Session Manager?

**SSM Session Manager** provides a secure, audited, **browser-based or CLI shell** to EC2 instances and on-premises servers — without requiring:

- SSH keys
- Bastion hosts (jump servers)
- Open inbound security group rules (port 22 or 3389)

> "Session Manager = SSH without SSH."

## How It Works

```
User (Browser / AWS CLI)
        ↓  HTTPS
AWS Systems Manager Service
        ↓  Outbound HTTPS (443) from instance
EC2 Instance (SSM Agent)
```

- The **SSM Agent** on the instance establishes an **outbound** HTTPS connection to the SSM service
- The user's session request is routed through SSM — no direct network path from user to instance required
- Works for instances in **public subnets**, **private subnets** (with NAT or VPC endpoints), and even instances with **no internet access** (using VPC endpoints)

## Access Control

- Access is controlled via **IAM** — not SSH key pairs
- Grant/revoke access by attaching/detaching IAM policies
- Use IAM conditions to restrict which instances a user can connect to (`ssm:resourceTag/Environment = prod`)
- No need to manage SSH key distribution or rotation

## Auditing and Logging

All Session Manager sessions are **logged** by default:

- **CloudTrail** — records the `StartSession` API call (who started, when, from where)
- **S3** — optionally store complete session transcripts (every command and output)
- **CloudWatch Logs** — optionally stream session activity in real-time

## Port Forwarding

Session Manager supports **port forwarding** — tunnel local ports to remote services through an SSM session:

```bash
# Forward local port 3389 to RDS/EC2 port 3389 (for RDP without opening SG)
aws ssm start-session \
  --target i-1234567890abcdef0 \
  --document-name AWS-StartPortForwardingSession \
  --parameters '{"portNumber":["3306"],"localPortNumber":["3306"]}'
```

- Common use: access RDS, Redis, or internal services from a developer's local machine without VPN
- Also used for Windows RDP sessions without opening port 3389

## Session Manager vs SSH/Bastion

| Aspect | SSH / Bastion | Session Manager |
|---|---|---|
| Inbound SG rule required | Yes (port 22) | **No** |
| SSH key management | Yes | **No** |
| Bastion host needed | Yes | **No** |
| Access control | SSH keys + SG | **IAM** |
| Audit logging | Manual (if configured) | **Built-in (CloudTrail, S3, CWL)** |
| Works for private instances | Requires VPN/bastion | **Yes (with NAT or VPC endpoints)** |
| Port forwarding | SSH tunneling | **Native support** |

## Requirements

1. **SSM Agent** installed and running on the target instance
2. IAM role attached to the instance with `AmazonSSMManagedInstanceCore` policy
3. Outbound HTTPS (443) allowed from the instance to SSM endpoints
4. IAM user/role connecting must have `ssm:StartSession` permission

## Key Points / Exam Tips

- Session Manager = **no SSH port, no key pairs, no bastion** — always the right answer for "secure access without SSH"
- Access is managed through **IAM** — not network-level rules
- All sessions are audited in **CloudTrail** automatically
- **Port forwarding** via Session Manager avoids the need to open firewall ports for databases and internal services
- For completely private instances: configure **VPC Interface Endpoints** for `ssm`, `ssmmessages`, `ec2messages`
- Session transcripts can be stored in **S3** for compliance

## Trigger Words

| Keyword | Think |
|---|---|
| "Connect to EC2 without SSH keys" | Session Manager |
| "No bastion host required" | Session Manager |
| "Audit all shell sessions" | Session Manager + S3/CloudWatch Logs |
| "Access RDS in private subnet from laptop" | Session Manager port forwarding |
| "Remove SSH inbound rule from security group" | Session Manager |
