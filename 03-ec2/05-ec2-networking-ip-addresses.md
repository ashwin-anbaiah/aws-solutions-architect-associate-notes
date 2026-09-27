# EC2 Networking and IP Addresses

## The Three IP Address Types

Every EC2 instance launched in a VPC can have up to three types of IP addresses:

| Type | Assignment | Persistence | Purpose |
|---|---|---|---|
| **Private IP** | From the VPC subnet CIDR range | Stays until terminated | Communication within the VPC and over VPN/Direct Connect to on-premises |
| **Public IP** | From AWS's pool of public IPs | Lost on Stop → Start | Internet-accessible address; not a static address |
| **Elastic IP** | From AWS's pool; allocated to your account | Persists until released manually | Static public IP that survives Stop → Start |

---

## Private IP

- Allocated from the **subnet's CIDR range** (e.g., if subnet is `10.0.1.0/24`, instance gets something like `10.0.1.45`)
- **Persists as long as the instance is alive** — survives stop/start/reboot
- Only changes when the instance is terminated
- Used for all communication inside the VPC (between EC2 instances, to RDS, to ECS, etc.)

---

## Public IP

- Allocated from **Amazon's pool of public IPs** — not from a specific user's allocation
- AWS automatically assigns a public IP if the subnet has "Auto-assign Public IP" enabled
- **Critical behavior**: Public IP is **released when the instance is stopped** and a **new one is assigned when started again**
- This means every time you stop and start an instance, your public IP changes — which breaks DNS records and any firewall rules pointing to it
- Use an Elastic IP when you need a stable public IP

---

## Elastic IP (EIP)

An **Elastic IP** is a **static public IPv4 address** that you allocate to your AWS account. You can:
- Assign it to an EC2 instance (replaces the auto-assigned public IP)
- Re-assign it to a different instance (e.g., if the original instance fails, point the EIP to a replacement)
- Release it back to AWS

**EIP facts**:
- You can have up to **5 EIPs per region** by default (soft limit, can be increased)
- **You pay for an EIP if it is allocated but NOT attached to a running instance** — AWS charges to discourage hoarding
- While attached to a running instance: free

**Use case pattern**: An application with a single EC2 instance that needs a fixed public IP for DNS or firewall whitelisting. When the instance is replaced (e.g., after a failure), re-attach the EIP to the new instance — the IP stays the same from the external perspective.

---

## Internet Gateway and NAT — How IPs Work

### Public Subnet Instances (have a public IP)

The **Internet Gateway** performs Network Address Translation (NAT) for public-subnet instances:
- Your instance has private IP `10.0.1.45` internally
- The Internet Gateway translates that to the public IP `54.23.45.67` when sending traffic to the internet
- Responses come back to the public IP, the IGW translates back to `10.0.1.45`

The instance itself **only knows its private IP** — the public IP translation happens transparently at the IGW.

### Private Subnet Instances (no public IP)

Private subnet instances cannot directly reach the internet. They go through a **NAT Gateway**:
- NAT Gateway is in a public subnet
- Private instances send outbound traffic to NAT Gateway, which translates the source IP to the NAT Gateway's own public IP
- Return traffic comes back to NAT Gateway, which forwards it to the private instance

---

## Default VPC Structure

When you create an AWS account, each Region has a **Default VPC** pre-created:
- CIDR: `172.31.0.0/16`
- One subnet per AZ (e.g., `172.31.0.0/24`, `172.31.1.0/24`, `172.31.2.0/24`)
- All subnets in the default VPC have "Auto-assign Public IP" enabled
- Has an Internet Gateway already attached
- Has a route table routing `0.0.0.0/0` to the Internet Gateway

This means EC2 instances launched in the default VPC immediately get a public IP and internet access — convenient for learning, but not production-safe.

---

## Key Points / Exam Tips

- **Private IP** = persistent within instance lifecycle; internal communication
- **Public IP** = temporary; lost on Stop/Start
- **Elastic IP** = static public IP; persists until released; free when attached to running instance; charged when unattached
- **Public IP translation happens at the Internet Gateway** — the instance only knows its private IP
- **NAT Gateway handles outbound internet for private subnets** — not for inbound connections
- **Default VPC** has public subnets and auto-assigned public IPs — fine for dev, not for production

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Static public IP for EC2" | Elastic IP |
| "Public IP changes after stop/start" | Expected behavior — use Elastic IP to prevent this |
| "Instance in private subnet needs internet access" | NAT Gateway in public subnet |
| "Charge for unused Elastic IP" | Yes — AWS charges for allocated-but-unattached EIPs |
| "Instance only knows its private IP" | True — Internet Gateway does NAT for public IPs |
