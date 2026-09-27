# AWS Client VPN

## What is AWS Client VPN?

**AWS Client VPN** is a managed, client-based VPN service that allows **individual users** to securely connect to AWS VPC resources (and optionally on-premises resources) from their workstations over the internet using **OpenVPN**.

---

## Key Concepts

| Concept | Detail |
|---|---|
| **Protocol** | OpenVPN (standard VPN client protocol) |
| **Authentication** | Active Directory, SAML-based, certificate-based, or federated identity |
| **Encryption** | TLS — encrypted over the public internet |
| **Client VPN Endpoint** | AWS resource deployed in a specific VPC/subnet |
| **Target Network** | The VPC subnet(s) that VPN clients can access |

---

## How It Works

1. Admin creates a Client VPN Endpoint and associates it with a VPC subnet
2. Clients download and install the **AWS VPN Client** (based on OpenVPN)
3. Client authenticates (AD, certificate, SAML), then connects
4. VPN client receives a private IP from the Client VPN CIDR range
5. Client traffic is routed through the VPN to the target VPC — the client is "inside" the network

```
User Workstation
    → (TLS encrypted, public internet)
        → Client VPN Endpoint (in public subnet of VPC)
            → Private resources in the VPC (EC2, RDS, etc.)
```

---

## Site-to-Site VPN vs. Client VPN

| Feature | Site-to-Site VPN | Client VPN |
|---|---|---|
| Connects | Entire on-premises network to AWS | Individual user/laptop to AWS |
| Protocol | IPSec | OpenVPN (TLS) |
| User authentication | Shared key / certificate (device-level) | AD / SAML / certificate (per user) |
| Use case | Corporate data center to VPC | Remote workers accessing VPC |
| Setup | Customer Gateway + VGW | Client VPN Endpoint + user credentials |

---

## Access Control

- Attach **Authorization Rules** (like ACL entries) to control which users/groups can access which network ranges
- Integrates with Active Directory or Okta (via SAML) for user-level access control
- Security Groups can also be attached to the Client VPN Endpoint

---

## Split Tunneling

- By default: **all traffic** from the client is routed through the VPN (full tunnel)
- With **split tunneling enabled**: only traffic destined for the VPC CIDR goes through the VPN; all other traffic (e.g., internet browsing) goes directly out the client's local network
- Split tunneling reduces VPN load and improves performance for internet-bound traffic

---

## Key Points / Exam Tips

- Client VPN = **individual user connectivity** (remote worker → VPC)
- Site-to-Site VPN = **network-to-network connectivity** (on-premises network → VPC)
- Client VPN uses **OpenVPN** — clients must install an OpenVPN-compatible VPN client
- Traffic goes **over the public internet, encrypted with TLS** — not over a dedicated link
- **Split tunneling** is a configuration option to optimize bandwidth (only VPC traffic goes through the tunnel)
- Client VPN supports access to both VPC resources AND on-premises resources (if Site-to-Site VPN or DX is connected to the VPC)

---

## Trigger Words

| Exam says... | Think... |
|---|---|
| "Remote employees securely access VPC" | Client VPN |
| "Individual user connects to private AWS resources" | Client VPN |
| "OpenVPN-based access" | Client VPN |
| "Network-to-network private connectivity" | Site-to-Site VPN (not Client VPN) |
| "Split tunneling for VPN" | Client VPN split tunnel configuration |
