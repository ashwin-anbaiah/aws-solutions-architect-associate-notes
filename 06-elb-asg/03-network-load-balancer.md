# Network Load Balancer (NLB)

## What is NLB?

The **Network Load Balancer** operates at **OSI Layer 4** (the Transport layer). It routes traffic based on IP protocol data — specifically the 5-tuple (source IP, source port, destination IP, destination port, protocol). It does not inspect HTTP content.

**Analogy:** NLB is like a toll booth that reads only the license plate (IP/port), not the passengers inside the car.

---

## Core Features

- **Protocols**: TCP, UDP, TLS
- **Targets**: EC2 instances, IP addresses, containers, and **Application Load Balancer** (ALB can be an NLB target)
- Lambda functions are **NOT supported** as NLB targets
- Handles **millions of requests per second** with ultra-low latency
- **One static IP per AZ** — can assign **Elastic IPs** to each AZ endpoint
- Useful when clients need to **whitelist specific IPs** in their firewall

---

## Key Differentiators from ALB

| Feature | NLB | ALB |
|---|---|---|
| OSI Layer | Layer 4 | Layer 7 |
| Protocols | TCP, UDP, TLS | HTTP, HTTPS, HTTP/2, gRPC |
| Static IP | Yes (one per AZ, can use Elastic IP) | No (DNS name only) |
| Client IP preservation | Yes (pass-through) | No (use X-Forwarded-For) |
| Lambda as target | No | Yes |
| ALB as target | Yes | No |
| Cross-zone LB default | Off | On |

---

## Client IP Preservation

- NLB is a **pass-through** load balancer — the original client IP is visible to the backend target
- If a **TLS listener** is used and TLS terminates at the NLB, the backend sees the NLB IP, not the client IP
- To recover the client IP when TLS terminates at the NLB, enable **Proxy Protocol v2**

## Proxy Protocol v2

When enabled, the NLB injects a small binary header into the TCP packet containing:
- Original source IP and port (client)
- Destination IP and port (NLB)
- Protocol type (TCP/UDP/TLS)
- Optional TLS metadata

---

## Sticky Sessions

- NLB supports sticky sessions using the **client's source IP address**
- Uses a **Flow Hash algorithm** (5-tuple: source IP + source port + protocol + dest IP + dest port) to consistently route the same flow to the same target
- Good for **long-lived TCP connections** (gaming, IoT, database connections)

---

## VPC PrivateLink Integration

- NLB is the required front-end when creating a **VPC Endpoint Service** (PrivateLink)
- Allows you to expose your service to consumers in other VPCs without peering — traffic stays private and on AWS backbone

---

## NLB as an ALB Target

Use case: Client needs **static IPs** to whitelist in their corporate firewall for a web application that needs ALB content-based routing.

```
Client (corporate) → NLB (static Elastic IPs) → ALB (content-based routing) → EC2
```

---

## Key Points / Exam Tips

- NLB is the **only load balancer supporting UDP** — ALB does not support UDP
- NLB provides **static IPs per AZ** — the only ELB type with true fixed IPs
- **Cross-zone load balancing is OFF by default** for NLB (can be enabled, may incur inter-AZ data transfer cost)
- NLB has **no Security Groups** — traffic filtering is done at the target's Security Group
- NLB supports **TLS termination** (SNI works on NLB too)
- For PrivateLink endpoint services, NLB is required in the provider VPC
- NLB maintains **connection state** (TCP connection preservation) — unlike ALB which opens new connections to backends

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Static IP / whitelist IP in corporate firewall" | NLB (one static IP per AZ, can use Elastic IPs) |
| "UDP traffic" | NLB (the ONLY load balancer supporting UDP) |
| "Ultra-low latency / millions of requests per second" | NLB |
| "Gaming, IoT, streaming, real-time systems" | NLB |
| "SSH / bastion host behind a load balancer" | NLB (SSH is TCP port 22) |
| "PrivateLink endpoint service" | NLB required in the provider VPC |
| "Client IP visible at backend" | NLB (pass-through), or ALB with X-Forwarded-For |
| "Proxy Protocol v2" | NLB (used when TLS terminates at NLB to pass client IP) |
