# AWS CLI, SDK, and CloudShell

## How You Interact with AWS Programmatically

AWS exposes all its functionality through **REST HTTP APIs**. The Management Console is just a web application calling these APIs. Every action you take in the console — launching an EC2 instance, creating an S3 bucket — is an API call.

The CLI and SDKs are simply different wrappers around the same underlying API layer.

```
User/Application → AWS CLI / SDK → REST API (HTTPS) → IAM authorization → AWS Service
```

---

## AWS CLI (Command Line Interface)

The **AWS CLI** is a unified command-line tool to interact with AWS services from your terminal.

### Setup

1. Install the CLI on your workstation: `https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html`
2. Configure with your credentials:
   ```
   aws configure
   > AWS Access Key ID: AKIAIOSFODNN7EXAMPLE
   > AWS Secret Access Key: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
   > Default region name: ap-south-1
   > Default output format: json
   ```

### Common CLI Commands

```bash
aws ec2 describe-instances           # List EC2 instances
aws s3 ls                            # List S3 buckets
aws s3 cp myfile.txt s3://my-bucket/ # Upload a file
aws iam get-user                     # Get details about the current IAM user
```

### How CLI Authenticates

- Locally: uses the access key credentials stored in `~/.aws/credentials`
- On EC2: can use the IAM role attached to the instance (no credentials file needed)
- AWS CLI uses **SigV4 (Signature Version 4)** to sign API requests

**The CLI authenticates to IAM, and IAM checks permissions before allowing the action.**

---

## AWS SDK (Software Development Kit)

The **AWS SDK** wraps the low-level REST APIs in language-specific libraries so developers can interact with AWS services using native code patterns rather than raw HTTP calls.

### Available SDKs

| Language | SDK |
|---|---|
| Python | Boto3 |
| Java | AWS SDK for Java v2 |
| Node.js | AWS SDK for JavaScript v3 |
| .NET (C#) | AWS SDK for .NET |
| Go | AWS SDK for Go v2 |
| Ruby | AWS SDK for Ruby v3 |
| C++ | AWS SDK for C++ |

**Note**: The AWS CLI itself uses the Python (Boto3) SDK internally.

### SDK in Action (Python/Boto3)

```python
import boto3

s3 = boto3.client('s3')
s3.upload_file('myfile.txt', 'my-bucket', 'myfile.txt')  # uploads to S3
```

When this runs on an EC2 instance with an IAM role, Boto3 automatically fetches temporary credentials from the instance metadata service — no credentials configuration needed in the code.

### SDK Authentication — Credential Provider Chain

The SDK looks for credentials in this order (first one wins):
1. Environment variables (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)
2. `~/.aws/credentials` file
3. IAM role attached to the EC2 instance / Lambda function / container
4. ECS task role
5. Default STS web identity (for EKS/Fargate workloads)

---

## AWS CloudShell

**AWS CloudShell** is a browser-based shell environment embedded in the AWS Management Console. It gives you a pre-authenticated terminal without needing to install anything.

### Key Features

- **No setup**: opens directly in the AWS Console browser session
- **Pre-authenticated**: uses your current console session's IAM credentials
- **AWS CLI pre-installed**: and other tools (git, python, etc.)
- **Persistent storage**: 1 GB of free persistent storage per Region
- **Available in select Regions**: check the console for availability

### When to Use CloudShell

- Quick one-off CLI commands without leaving the browser
- Troubleshooting from a machine where the CLI isn't installed
- Learning/demos without local CLI configuration

### CloudShell Limitations

- Not a full VM — limited CPU and memory
- Limited to Regions where it's available
- Session times out after inactivity

---

## Key Points / Exam Tips

- **CLI and SDK use the same IAM credentials and authorization as the console** — same policies apply
- **SDK Credential Chain**: the SDK automatically picks up IAM role credentials on EC2/Lambda — no hardcoded keys needed
- **Never hardcode access keys in application code** — use IAM roles and the credential chain instead
- **CloudShell uses your console session's credentials** — no extra configuration needed
- **AWS CLI is built on Boto3 (Python SDK)** — they share the same underlying authentication

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Programmatic access to AWS from scripts" | AWS CLI + access keys |
| "Application needs to call AWS APIs" | AWS SDK (language-specific) |
| "EC2 app should not have hardcoded credentials" | IAM role + credential chain |
| "Browser-based CLI, no installation" | AWS CloudShell |
| "Boto3" | Python AWS SDK (what the CLI uses too) |
