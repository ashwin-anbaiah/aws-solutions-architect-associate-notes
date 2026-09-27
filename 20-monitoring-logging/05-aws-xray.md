# AWS X-Ray

## What is AWS X-Ray?

**AWS X-Ray** is a **distributed tracing** service that collects data about requests as they travel through your application, providing end-to-end visibility across microservices and distributed systems.

> "X-Ray lets you trace a single user request as it flows from service to service — and find where it breaks or slows down."

## The Problem X-Ray Solves

In a microservices architecture, a single user request can touch dozens of services:

```
User → API Gateway → Order Service → Billing Service → DB
                  ↘ Notification Service → SQS → Email Lambda
```

Traditional logging shows what happened in each service but doesn't connect the dots. X-Ray **stitches the entire journey together**.

## Core Concepts

### Traces

A **trace** is the complete record of a single request's journey through the application — from the initial entry point to the final response.

- A trace has a unique **Trace ID**
- Contains all the **segments** generated along the request path

### Segments

A **segment** represents the work done by a single service or component for a given request.

- Each service creates one segment per request
- Contains: request start/stop times, errors, HTTP status codes, metadata

### Subsegments

A **subsegment** provides more granular detail within a segment.

- Example: inside the Order Service segment, subsegments for each DynamoDB call and external API call
- Useful for pinpointing exactly which database query or downstream call is slow

### Service Map

The **Service Map** is a visual diagram of your application's architecture as seen through X-Ray — showing:

- All services involved in handling requests
- Response times between services
- Error rates per service
- Request volume

### Sampling

- X-Ray does **not** record every request by default — it uses **sampling** to reduce cost and overhead
- Default sampling rule: **1 request per second + 5% of additional requests**
- Custom sampling rules can be configured (by service name, URL, method, etc.)
- Reservoir + fixed rate model

## How X-Ray Works

```
Application Code (instrumented with X-Ray SDK)
        ↓  sends trace data
X-Ray Daemon (background process on the instance)
        ↓  batches and sends to AWS
AWS X-Ray Service
        ↓
X-Ray Console (Service Map, Traces, Analytics)
```

### X-Ray SDK

- Embedded in application code (Java, Python, Node.js, Go, Ruby, .NET)
- Automatically instruments incoming/outgoing HTTP calls, AWS SDK calls, SQL queries
- Can be manually instrumented for custom subsegments

### X-Ray Daemon

- A **lightweight background process** that runs alongside your application
- Listens on **UDP port 2000** for trace data from the SDK
- Buffers and sends data to the X-Ray API in batches
- Pre-installed on Elastic Beanstalk environments
- On ECS: run the daemon as a **sidecar container**
- On EC2: install and run manually

## AWS Services That Integrate with X-Ray

- AWS Lambda
- Amazon EC2 / Elastic Beanstalk
- Amazon ECS / EKS
- API Gateway
- ALB
- AWS App Mesh
- AWS Amplify

## Use Cases

- Debug **slow responses** — trace which service/call is the bottleneck
- Troubleshoot **increased error rates** — find where failures originate
- Map request paths across services
- Verify SLA compliance — are requests completing within expected time?
- Identify which users are affected by a service disruption

## Key Points / Exam Tips

- X-Ray provides **distributed tracing** — not just per-service monitoring
- The **X-Ray Daemon** collects SDK data locally and forwards to AWS (UDP 2000)
- **Sampling** controls what percentage of requests are traced (reduces cost)
- **Service Map** = visual topology of your application with latency and error data
- Trace = full journey; Segment = one service; Subsegment = one operation within a service
- For **ECS**: run X-Ray daemon as a **sidecar container** in the same task
- For **Lambda**: enable X-Ray tracing in the Lambda function configuration (no daemon needed)
- X-Ray is part of **CloudWatch ServiceLens** (unified view of logs, metrics, traces)

## Trigger Words

| Keyword | Think |
|---|---|
| "Trace request across microservices" | AWS X-Ray |
| "Find bottleneck in distributed app" | AWS X-Ray |
| "Service Map / application topology" | AWS X-Ray |
| "X-Ray Daemon on EC2/ECS" | X-Ray background process (UDP 2000) |
| "Distributed tracing" | AWS X-Ray |
| "Sampling rules" | X-Ray sampling configuration |
