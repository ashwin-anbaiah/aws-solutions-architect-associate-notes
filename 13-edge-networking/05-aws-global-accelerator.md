# AWS Global Accelerator

## What is AWS Global Accelerator?

**AWS Global Accelerator** is a global networking service that improves the availability and performance of applications by routing traffic through the **AWS global network** instead of the public internet.

- Uses **AWS edge locations** (like CloudFront) but serves a different purpose — network routing, not content caching
- Allocates **2 static Anycast IP addresses** that serve as fixed entry points to your application
- Traffic enters the AWS network at the nearest edge location, then travels over the **low-latency AWS backbone** to your endpoint
- Supports **TCP and UDP** protocols — not limited to HTTP/HTTPS
- Performs **health checks** on endpoints and performs **automatic failover** across regions

## How it Works

1. Client connects to one of the **2 Anycast IPs**
2. Anycast routing sends traffic to the **nearest edge location**
3. Traffic travels over the **AWS backbone network** to the closest healthy endpoint
4. On endpoint failure, Global Accelerator **automatically reroutes** to the next healthy endpoint

## Supported Endpoints

- **Application Load Balancer (ALB)**
- **Network Load Balancer (NLB)**
- **EC2 instances**
- **Elastic IP addresses**

## Key Features

- **Static Anycast IPs**: 2 fixed IPs that never change — easy to whitelist at firewalls/clients
- **Health checks**: Continuous monitoring; automatic failover within 30 seconds
- **TCP and UDP support**: Suitable for gaming, VoIP, IoT (MQTT), video streaming
- **DDoS protection**: Built-in via **AWS Shield Standard**
- **Traffic dials**: Shift traffic weights between endpoint groups for blue/green deployments

## CloudFront vs Global Accelerator

| Feature | CloudFront | Global Accelerator |
|---|---|---|
| **Primary function** | CDN — content caching at edge | Network routing optimization |
| **How to access** | DNS name | 2 Static Anycast IPs |
| **Caching** | Yes (major feature) | No |
| **TCP/UDP support** | HTTP/HTTPS only | Yes (TCP and UDP) |
| **Failover** | Limited (DNS-based, slow TTL) | Yes — fast health check-based |
| **Best for** | Websites, media streaming, APIs | VoIP, gaming, IoT, non-HTTP apps |
| **DDoS protection** | Yes (Shield) | Yes (Shield) |
| **IP addresses** | Changes with DNS | Fixed static IPs |

## Key Points / Exam Tips

- Global Accelerator provides **2 fixed static IPs** — use when clients need to whitelist IPs or when DNS-based routing is too slow
- It does **not cache content** — it only optimizes network routing; do not confuse it with CloudFront
- Global Accelerator is the answer when: "non-HTTP protocol (TCP/UDP)", "static IPs required", "fast failover across regions", "VoIP/gaming/IoT"
- Both CloudFront and Global Accelerator use AWS edge locations, but they work differently
- Global Accelerator health checks are faster than DNS TTL-based failover — use it when RTO matters
- The 2 Anycast IPs are **always the same** regardless of traffic volume or endpoint changes

## Trigger Words

- "VoIP / gaming / IoT (MQTT) global performance" → Global Accelerator
- "Static IPs for global application" → Global Accelerator
- "Fast failover across AWS regions" → Global Accelerator
- "TCP/UDP protocol, not HTTP" → Global Accelerator
- "Whitelist 2 IP addresses for global app" → Global Accelerator
- "CDN / cache static content globally" → CloudFront (not Global Accelerator)
