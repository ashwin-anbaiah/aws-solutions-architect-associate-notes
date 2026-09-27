# AWS Transfer Family

## What Is Transfer Family?

**AWS Transfer Family** is a fully managed service that provides SFTP, FTPS, FTP, and AS2 endpoints backed by Amazon S3 or Amazon EFS. It lets end users and partner systems continue using their familiar file transfer clients while the data lands directly in AWS storage — no custom server infrastructure required.

---

## Supported Protocols

| Protocol | Full Name | Notes |
|---|---|---|
| **SFTP** | SSH File Transfer Protocol | Most common; encrypted, widely supported |
| **FTPS** | FTP over SSL/TLS | Encrypted FTP; older enterprise environments |
| **FTP** | File Transfer Protocol | Unencrypted; legacy systems only |
| **AS2** | Applicability Statement 2 | B2B EDI document exchange standard |

---

## Supported Storage Backends

| Backend | Notes |
|---|---|
| **Amazon S3** | Standard S3 bucket; all storage classes available |
| **Amazon EFS** | Linux/NFS shared filesystem |

> **Exam trap:** Transfer Family does **NOT** support FSx for Windows as a storage backend — only S3 and EFS.

---

## User Authentication Options

- **IAM policies** — native AWS identity
- **Active Directory** — on-prem or AWS Managed Microsoft AD
- **LDAP** — directory-based identity
- **Third-party identity providers** — via custom Lambda authorizer
- **Amazon Cognito** — for web/mobile user federation

---

## Architecture Pattern

```
Vendors / Partners / Legacy Apps
         |
    SFTP/FTP/FTPS client
         |
  AWS Transfer Family endpoint (managed)
         |
  Amazon S3 bucket  or  Amazon EFS filesystem
```

- Each user/vendor can be scoped to a specific **S3 prefix** or **EFS home directory** using an IAM role.
- Per-vendor IAM roles enforce least-privilege access isolation.

---

## Use Cases

- **B2B file exchange with partners** — vendors upload EDI or data files via SFTP without changing their workflow
- **EDI document transfer** — replace on-prem AS2 servers with managed AS2 endpoints
- **Replacing on-premises FTP/SFTP servers** — eliminate infrastructure management
- **Legacy app integration** — apps that only know FTP/SFTP continue to work; backend silently becomes S3

---

## DataSync vs Transfer Family (Side-by-Side)

| Dimension | AWS DataSync | AWS Transfer Family |
|---|---|---|
| Users | Systems/automated processes | Human users, partner organizations, legacy apps |
| Initiation | Scheduled/automated | User-driven interactive transfers |
| Protocols | NFS, SMB, HDFS | SFTP, FTPS, FTP, AS2 |
| Destinations | S3, EFS, all FSx variants | S3 and EFS only |
| User access control | Not a concern | Central user auth and access management |
| Typical scenario | Migration, data sync | Replacing FTP servers, B2B file drop |

---

## Key Points / Exam Tips

- Transfer Family is for **interactive, user-driven file transfers** using legacy protocols — DataSync is for automated bulk sync.
- The service provides **fully managed endpoints** — no EC2 servers to run, patch, or scale.
- Transfer Family supports **only S3 and EFS** as storage — not FSx.
- **Per-vendor IAM roles** scoped to specific S3 prefixes = the standard least-privilege pattern for multi-vendor access.
- AS2 support is for B2B EDI scenarios; exam may specifically mention "EDI" or "AS2" to point here.

---

## Trigger Words

| Phrase | Answer |
|---|---|
| "Vendors use legacy SFTP, store in S3, no infrastructure" | AWS Transfer Family |
| "Replace on-premises FTP server" | AWS Transfer Family |
| "B2B file exchange, SFTP, fully managed" | AWS Transfer Family |
| "EDI / AS2 document transfer" | AWS Transfer Family (AS2 support) |
| "Users upload via SFTP, data lands in EFS" | AWS Transfer Family |
| "SFTP + per-vendor access isolation to S3 prefixes" | Transfer Family + scoped IAM roles per user |
