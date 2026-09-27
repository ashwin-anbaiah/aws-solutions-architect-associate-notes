# Security Groups

## What Is a Security Group?

A **Security Group** is a virtual firewall for EC2 instances. It controls inbound and outbound traffic at the instance level (technically at the ENI/network interface level).

Think of it as a bouncer for your instance: it checks every connection request against a list of rules before letting traffic in or out.

---

## Security Group Rules

Security groups use **allow-only rules** — you can never explicitly block traffic. The only way to block traffic is to not have a rule that allows it.

### Rule Components

| Component | Description | Example |
|---|---|---|
| **Type** | Protocol shortcut | SSH, HTTP, HTTPS, Custom TCP |
| **Protocol** | TCP, UDP, ICMP, or All | TCP |
| **Port Range** | Single port or range | 22, 80, 443, 3000-3999 |
| **Source/Destination** | Where traffic comes from (inbound) or goes to (outbound) | `0.0.0.0/0`, `10.0.0.0/16`, another Security Group ID |

### Valid Rule Sources/Destinations

- IP address (exact): `5.6.7.8/32`
- CIDR range: `203.55.22.0/24`
- **Another Security Group ID** (very powerful — "allow traffic from any instance in SG-X")
- Prefix List (managed list of AWS service IPs)

**Invalid sources** (exam trap): Internet Gateway ID, Subnet ID, Route Table ID — these are NOT valid SG sources.

---

## Default Behavior

| Direction | Default Rule |
|---|---|
| **Inbound** | All traffic **blocked** by default (no inbound rules = no connections accepted) |
| **Outbound** | All traffic **allowed** by default (default outbound rule: `All traffic → 0.0.0.0/0`) |

When you create a new security group, you must explicitly add inbound rules to allow any incoming connections.

---

## Stateful Behavior

Security groups are **stateful** — this is crucial to understand:

- If an **inbound request is allowed**, the **response is automatically allowed back** — you don't need a separate outbound rule for it
- If you initiate an **outbound connection** and the rule allows it, the **response comes back automatically** — no extra inbound rule needed

This is different from NACLs (Network ACLs), which are stateless and require explicit rules for both directions.

```
Client → SG allows inbound TCP:80 → HTTP request reaches EC2
EC2   → HTTP response automatically allowed out → Client receives response
                  (no outbound rule needed for this return traffic)
```

---

## Attaching Security Groups

- **One instance can have multiple security groups** (up to 16 SGs per instance with up to 1000 rules combined)
- **One security group can be attached to multiple instances**
- Rules from all attached SGs are combined — if any SG allows the traffic, it's allowed

---

## Referencing Another Security Group as a Source

This is one of the most powerful SG features:

```
Web SG: allows inbound 443 from 0.0.0.0/0
DB SG:  allows inbound 3306 from Web SG (Security Group ID)
```

The DB security group allows MySQL connections only from instances that are in the Web SG. When new web instances are added (via Auto Scaling), they inherit the Web SG and automatically get access to the database — no IP-based rule update needed.

**Why this matters**: Using SG-to-SG references is more dynamic and secure than using CIDR ranges, especially in Auto Scaling environments where instance IPs change.

---

## Common Port Reference

| Service | Protocol | Port |
|---|---|---|
| SSH | TCP | 22 |
| SFTP | TCP | 22 |
| HTTP | TCP | 80 |
| HTTPS | TCP | 443 |
| RDP | TCP | 3389 |
| FTP | TCP | 21 |
| MySQL | TCP | 3306 |
| PostgreSQL | TCP | 5432 |
| Redis | TCP | 6379 |
| MongoDB | TCP | 27017 |

---

## Security Groups vs NACLs — Quick Comparison

| | Security Group | Network ACL |
|---|---|---|
| Level | Instance (ENI) | Subnet |
| Rules | Allow only | Allow AND Deny |
| State | Stateful (return traffic auto-allowed) | Stateless (must allow both directions) |
| Evaluation | All rules evaluated together | Rules evaluated in order (lowest number first); first match wins |
| Default | Block all inbound, allow all outbound | Allow all inbound and outbound |

**Key rule**: If you need to explicitly **block** a specific IP address, use a NACL (Security Groups can't deny).

---

## Key Points / Exam Tips

- **Allow-only** — security groups cannot deny/block, only allow
- **Stateful** — response traffic is automatically allowed (no extra rule needed)
- **All inbound blocked by default** — new SGs have no inbound rules
- **Another SG as source** — more dynamic than IP ranges for dynamic environments
- **Multiple SGs per instance** — rules are combined; any allowing SG = traffic allowed
- **S3 has NO security groups** — common exam trap. S3 access is controlled by IAM policies and bucket policies, not SGs

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Block a specific IP address" | Use NACL, not Security Group (SGs can't deny) |
| "Allow only web tier to reach DB tier" | SG referencing another SG as the source |
| "Stateful firewall" | Security Group |
| "Response traffic automatically allowed" | Security Group (stateful) |
| "Security group on S3 bucket" | Invalid — S3 doesn't have security groups |
