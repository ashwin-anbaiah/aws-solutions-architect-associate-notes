# Gateway Load Balancer (GWLB)

## What is GWLB?

The **Gateway Load Balancer** is designed to deploy, scale, and manage **third-party virtual network appliances** — such as firewalls, intrusion detection/prevention systems (IDS/IPS), and deep packet inspection tools. Traffic is sent through these appliances transparently before reaching its destination.

**Analogy:** GWLB is like a security checkpoint at a building entrance — every visitor (packet) must pass through the checkpoint (network appliance) without the visitor knowing it happened.

---

## Key Characteristics

- Operates at **OSI Layer 3** (the Network/IP layer)
- Uses the **GENEVE protocol** on **port 6081** to encapsulate and forward packets between clients and appliances
- **Transparent to traffic**: does not modify source or destination IP addresses — appliances see the original IPs
- Traffic is sent to **GWLB Endpoints (GWLBe)** created in each VPC using AWS PrivateLink
- Integrates with partners: Aviatrix, Cisco, Fortinet, Palo Alto Networks, and others

---

## GWLB vs ALB vs NLB

| Feature | GWLB | ALB | NLB |
|---|---|---|---|
| OSI Layer | Layer 3 (IP) | Layer 7 (HTTP) | Layer 4 (TCP/UDP) |
| Protocol | GENEVE (port 6081) | HTTP/HTTPS/gRPC | TCP/UDP/TLS |
| Purpose | Traffic inspection via appliances | Content-based web routing | High-throughput transport |
| Modifies IPs? | No (transparent) | No | No |
| Targets | Network appliance instances | EC2, Lambda, IP, containers | EC2, IP, containers, ALB |

---

## How GWLB Works

```
Application VPC (source)
    |
    v
GWLB Endpoint (GWLBe) in source VPC
    |
    v (GENEVE encapsulation)
    v
Gateway Load Balancer (in Appliance/Security VPC)
    |
    v
Network Appliances (Firewall, IDS/IPS instances)
    |
    v (return traffic back via GWLB)
    v
GWLB Endpoint (GWLBe) in source VPC
    |
    v
Destination (e.g., internet or another VPC)
```

---

## Centralized Inspection Architecture

A common architecture centralizes all traffic inspection in a **dedicated Security VPC**:
- Multiple application VPCs route traffic through their **GWLBe** (GWLB Endpoint)
- All traffic flows to a single GWLB in the Security VPC
- The appliances inspect, block, or allow traffic
- Approved traffic is returned through the same path

This eliminates the need to deploy appliances in every VPC, reducing cost and operational overhead.

---

## Key Points / Exam Tips

- GWLB is NOT for web routing — it is exclusively for **traffic inspection** use cases
- The appliances see **original source and destination IPs** (transparent inspection)
- GWLB uses **GENEVE on port 6081** — memorize this for the exam
- GWLBe (endpoint) is the entry/exit point in each VPC, using AWS PrivateLink
- **Targets are the appliance instances** (EC2 running firewall software), not web servers
- Cross-zone load balancing is **OFF by default** for GWLB
- Works with Transit Gateway for centralized hub-and-spoke inspection across many VPCs

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Inline traffic inspection" | GWLB |
| "Third-party firewall / IDS / IPS" | GWLB |
| "Deep packet inspection" | GWLB |
| "Palo Alto / Fortinet / Cisco virtual appliance" | GWLB |
| "GENEVE protocol / port 6081" | GWLB |
| "Centralized security inspection VPC" | GWLB with centralized architecture |
| "Transparent to traffic / original IPs preserved" | GWLB |
