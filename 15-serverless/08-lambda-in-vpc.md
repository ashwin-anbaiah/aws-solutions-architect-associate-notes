# Lambda in VPC

## Default Lambda Behavior (Outside VPC)

By default, Lambda functions run in an **AWS-managed network environment outside your VPC**:

- Can access **public internet** and **public AWS services** natively
- **Cannot access** resources inside your VPC:
  - Private EC2 instances (private IPs)
  - RDS databases in private subnets
  - ElastiCache clusters
  - Internal ALBs or NLBs
  - OpenSearch Service in VPC

## Lambda Inside a VPC

You can configure a Lambda function to run **inside your VPC** to access private resources.

**How it works:**
- Lambda creates an **Elastic Network Interface (ENI)** in the specified VPC subnets
- The ENI gets a private IP from the subnet and connects Lambda to your VPC's network
- All standard VPC controls apply: **Security Groups**, **route tables**, **NACLs**

**Configuration required:**
1. Select the VPC
2. Select one or more **subnets** (place in private subnets for security)
3. Assign a **Security Group** to the ENI
4. Lambda's execution role needs `ec2:CreateNetworkInterface`, `ec2:DescribeNetworkInterfaces`, `ec2:DeleteNetworkInterface` permissions (or use the **AWSLambdaVPCAccessExecutionRole** managed policy)

## Internet Access for VPC Lambda

Lambda inside a VPC **cannot access the internet** or public AWS services directly through the VPC unless you configure it:

| Lambda Subnet | Route | Result |
|---|---|---|
| **Private subnet** | Route → NAT Gateway → Internet Gateway | Internet access + public AWS services |
| **Public subnet** | Route → Internet Gateway | **Still no internet** — Lambda ENIs do not get public IPs automatically |
| **Private subnet** | VPC Endpoints | Private access to AWS services (S3, DynamoDB, etc.) without internet |

**Key rule**: Lambda in a public subnet does NOT get internet access automatically. Always use a **private subnet + NAT Gateway** for internet access.

## VPC Endpoints for Lambda

To let a VPC Lambda access AWS services privately (without NAT Gateway):
- Create **VPC Interface Endpoints** or **VPC Gateway Endpoints** for the target services
- Example: S3 Gateway Endpoint allows Lambda in a private subnet to reach S3 without internet

## Architecture Pattern: Lambda + RDS (Most Common VPC Use Case)

```
Internet → API Gateway → Lambda (in private subnet, SG: allow all outbound)
                             ↓ (ENI)
                         VPC Private Subnet
                             ↓ (Security Group allows 3306 from Lambda SG)
                         RDS MySQL (private subnet)
```

1. Lambda's Security Group: allow outbound to RDS port (e.g., 3306)
2. RDS's Security Group: allow inbound from Lambda's Security Group

## Lambda Layers

Although not VPC-specific, **Lambda Layers** are commonly used with VPC Lambda for shared dependencies:

- Layers are ZIP archives containing libraries, custom runtimes, or configuration
- A function can reference up to **5 layers**
- Layers are versioned and can be shared across functions and accounts
- Common use: Python packages (pandas, numpy), Java dependencies, custom config files
- Reduces deployment package size — layers are separately managed

## Key Points / Exam Tips

- Lambda in VPC **cannot access the internet** unless you add a NAT Gateway in the private subnet
- Lambda in a **public subnet** still cannot access the internet — public subnets don't automatically assign public IPs to Lambda ENIs
- **Security Groups** apply to Lambda ENIs — use them to control access to RDS, ElastiCache, etc.
- Lambda must be in the VPC to access **private** resources (RDS, ElastiCache, internal ALB)
- Creating too many Lambda ENIs in a small subnet can exhaust available IPs — use /24 or larger subnets
- Use **RDS Proxy** between Lambda and RDS to handle connection pooling (Lambda can create thousands of connections per second)

## Trigger Words

- "Lambda needs to access RDS in private subnet" → Lambda in VPC
- "Lambda cannot connect to database" → Check if Lambda is in same VPC; check Security Groups
- "Lambda in VPC cannot access internet" → Add NAT Gateway in subnet route table
- "Lambda needs access to S3 without internet" → VPC Gateway Endpoint for S3
- "Shared libraries across Lambda functions" → Lambda Layers
- "Too many database connections from Lambda" → Use RDS Proxy
