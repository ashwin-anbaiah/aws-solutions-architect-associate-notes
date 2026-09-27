# Route 53 Health Checks

## What are Health Checks?

Route 53 **health checks** monitor the availability and health of your endpoints so that Route 53 can make intelligent routing decisions — routing traffic only to healthy resources.

- Works with: EC2 instances, ALBs, NLBs, CloudFront distributions, public IPs, and any publicly accessible endpoint
- Commonly combined with **Failover**, **Weighted**, **Latency**, and **Multi-value** routing policies

## Health Check Configuration

| Setting | Default | Range / Options |
|---|---|---|
| **Protocol** | HTTP | HTTP, HTTPS, TCP |
| **Check interval** | 30 seconds | 30 sec (standard) or 10 sec (fast — extra cost) |
| **Failure threshold** | 3 consecutive failures | 1–10 |
| **Healthy threshold** | 3 consecutive successes | 1–10 |
| **Path (HTTP/HTTPS)** | `/` | Any valid path |

## Health Check Types

### 1. Endpoint Health Check
- Route 53 sends health check requests to your endpoint
- Works for **publicly reachable** endpoints only (public IPs, domain names)
- Cannot directly health-check **private VPC resources** (private EC2 IPs, internal ALBs)

### 2. Calculated Health Check
- Aggregates the results of **multiple child health checks** into one parent check
- Supports AND/OR logic (e.g., healthy if 2 out of 3 child checks pass)
- Useful for complex multi-region setups

### 3. CloudWatch Alarm-based Health Check
- For **private resources** inside a VPC: create a **CloudWatch metric + alarm**, then create a health check that monitors the CloudWatch alarm state
- Allows Route 53 health checks on resources not directly reachable from the internet

## Private Resource Health Checks

Route 53 health checkers run from **outside your VPC** and cannot reach private IPs directly.

**Solution for private resources:**
1. Create a **CloudWatch metric** monitoring the private resource (e.g., EC2 CPU, custom app metric)
2. Create a **CloudWatch alarm** based on that metric
3. Create a Route 53 **health check linked to the CloudWatch alarm**
4. Route 53 uses the alarm state (OK / ALARM) to determine health

## Integration with Routing Policies

- **Failover routing**: Primary record is returned only when its health check passes; otherwise the secondary record is returned
- **Weighted routing**: Unhealthy records are removed from the weighted distribution
- **Latency routing**: Unhealthy regions are excluded from latency-based routing
- **Multi-value routing**: Only healthy endpoints (up to 8) are returned

## Key Points / Exam Tips

- Health checks work only for **publicly reachable endpoints** by default
- For **private resource health monitoring**, use a **CloudWatch Alarm-based health check** — this is a common exam scenario
- Default check interval is **30 seconds**; Fast health checks are **10 seconds** but cost more
- An endpoint is marked **unhealthy** after **3 consecutive failures** (default threshold)
- Health checks are required for **Failover routing** to work properly — without them, Route 53 cannot detect failures
- Route 53 health checkers originate from multiple AWS regions globally — if more than 18% of health checkers report unhealthy, the endpoint is considered unhealthy

## Trigger Words

- "Automatically route traffic away from unhealthy resource" → Route 53 health check + failover
- "Monitor private EC2 instance health in Route 53" → CloudWatch Alarm + Route 53 health check
- "DNS failover" → Route 53 Failover routing + health checks
- "Route 53 detect EC2 failure and switch to standby" → Failover routing policy + health check
