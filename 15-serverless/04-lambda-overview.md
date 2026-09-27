# AWS Lambda Overview

## What is AWS Lambda?

**AWS Lambda** is a serverless, event-driven compute service that runs your code without provisioning or managing servers (Function-as-a-Service).

- Write a function, upload code, Lambda handles everything else
- Scales **automatically** based on incoming request/event volume
- **Pay only for what you use**: number of invocations + execution duration + memory allocated
- Pricing unit: **GB-seconds** (memory GB × execution duration in seconds)

## Key Specifications

| Setting | Value |
|---|---|
| **Memory** | 128 MB – 10 GB (in 1 MB increments) |
| **Max execution timeout** | 15 minutes |
| **Storage (/tmp)** | Up to 10 GB ephemeral |
| **Deployment package** | Up to 50 MB (zipped), 250 MB (unzipped) |
| **Container image** | Up to 10 GB |
| **Concurrent executions** | 1,000 per region (default account limit) |
| **Supported languages** | Python, Node.js, Java, C# (.NET), Go, Ruby, PowerShell, and custom runtimes |

## How Lambda Works

1. An **event source** triggers Lambda (API Gateway, S3, SQS, EventBridge, etc.)
2. Lambda creates an **execution environment** (download code + initialize runtime — the "Init phase")
3. Lambda **invokes the handler function** (the "Invoke phase")
4. Function returns a response or writes to other services
5. Execution environment may be **reused** for subsequent invocations (warm start) or released

## Execution Environment Lifecycle

```
Cold Start:   [Download Code] → [Init Runtime] → [Init Handler] → [Invoke Handler]
Warm Start:                                                        [Invoke Handler]
```

- **Cold start**: First invocation or after a long idle — initialization adds latency
- **Warm start**: Environment reused from a previous invocation — faster response
- Lambda reuses environments but **does not guarantee** environment reuse

## Common Event Sources (50+ supported)

| Pattern | Event Sources |
|---|---|
| **Synchronous** | API Gateway, ALB, Cognito, Lambda function URL |
| **Asynchronous** | S3 events, SNS, EventBridge, CloudWatch Events |
| **Polling (ESM)** | SQS, Kinesis Data Streams, DynamoDB Streams, MSK, Kafka |

## IAM and Permissions

- Lambda uses an **IAM Execution Role** to access other AWS services (S3, DynamoDB, etc.)
- Callers need `lambda:InvokeFunction` permission on the Lambda function
- **Resource-based policies** on Lambda allow services (S3, SNS, API Gateway) to invoke it

## Common Use Cases

- **API backend**: Lambda + API Gateway for fully serverless REST APIs
- **File processing**: Trigger on S3 upload → process image, PDF, video
- **Message processing**: SQS queue consumer — read and process messages
- **Real-time stream processing**: Kinesis Data Streams consumer
- **Scheduled tasks**: EventBridge scheduled rules (cron) → Lambda
- **Event processing**: EventBridge pattern-based rules → Lambda
- **Database event handlers**: DynamoDB Streams → Lambda (indexing, notifications)

## Cost Comparison: EC2 vs Lambda

For a simple app with 10k requests/day (300k/month):
- **EC2 (m5.large x2 + ELB)**: ~$165/month (running 24/7 whether traffic exists or not)
- **Lambda + API Gateway**: ~$2.53/month (pay only when invoked)

Lambda is significantly cheaper for **variable or low-traffic workloads**.

## Key Points / Exam Tips

- Lambda is **stateless** — do not rely on in-memory state between invocations (unless using /tmp)
- Max timeout is **15 minutes** — for tasks longer than this, use ECS Fargate or AWS Batch
- **Default concurrency limit is 1,000 per region** across all functions
- Lambda pricing = **number of invocations + GB-seconds** (memory × duration)
- Lambda has **native CloudWatch integration** — logs go to CloudWatch Logs automatically
- Lambda + API Gateway is the canonical **fully serverless API pattern**
- Lambda cannot access private VPC resources by default — must place Lambda in VPC for that

## Trigger Words

- "Run code without managing servers" → Lambda
- "Event-driven compute" → Lambda
- "Pay per request / per invocation" → Lambda
- "Process S3 uploads automatically" → Lambda triggered by S3 event
- "Serverless API backend" → Lambda + API Gateway
- "Task completes in under 15 minutes" → Lambda (otherwise consider Fargate/Batch)
