# EC2 User Data

## What Is User Data?

**EC2 User Data** is a bootstrap script that runs automatically when an EC2 instance **first launches**. It allows you to automate the configuration of instances without manually SSHing in and running commands.

Think of it as a "setup script" that fires once, sets everything up, and then the instance is ready to serve traffic.

---

## What User Data Can Do

- Install OS updates and security patches
- Install application software (e.g., Apache, Nginx, Node.js)
- Download configuration files or application code
- Start and enable services (systemd, etc.)
- Register the instance with configuration management (Chef, Ansible, etc.)
- Any other shell command you'd run manually

---

## Example User Data Script

```bash
#!/bin/bash
# Update the system
dnf update -y

# Install Apache web server
dnf install -y httpd

# Start Apache and enable it on boot
systemctl start httpd
systemctl enable httpd

# Create a simple index page
echo "<h1>Hello from EC2 User Data!</h1>" > /var/www/html/index.html
```

This script installs a web server and serves a page — all automatically on first boot.

---

## Key Behaviors

| Behavior | Detail |
|---|---|
| **When it runs** | Once, on the very first boot/launch |
| **Who runs it** | Runs as the **root user** |
| **Script type** | Usually a shell script (#!/bin/bash) for Linux; PowerShell for Windows |
| **Size limit** | 16 KB |
| **Encoding** | Must be Base64-encoded when passed via CLI/API (the console handles this automatically) |
| **By default** | Runs ONCE only at first launch — does NOT run on every reboot |

### To Run on Every Boot (Non-Default)

You can configure user data to run on every start if needed (using cloud-init configuration), but this is the non-default behavior. The exam distinguishes between "run once" (default) and "run on every restart" (requires extra configuration).

---

## User Data vs AMI vs Instance Metadata

This is a common confusion point:

| | User Data | AMI | Instance Metadata |
|---|---|---|---|
| What it is | A startup script | A full disk image (OS + software) | Descriptive info about the instance |
| Contains data? | No (just instructions) | Yes (actual files, OS, applications) | No (just metadata) |
| Modifiable after launch? | Yes (but only runs again on reboot if reconfigured) | No | No |
| Can run scripts? | Yes — that's its purpose | No | No |
| Use case | First-boot automation | Pre-baked golden images | Self-identification (what's my IP? what's my instance ID?) |

**Instance metadata is NOT for running scripts.** If a question asks about running custom initialization scripts, the answer is User Data, not metadata.

---

## When to Use User Data vs AMI

| Situation | Use |
|---|---|
| Quick config change, small amount of setup | User Data |
| Large software stack, many packages to install | AMI (pre-bake it; avoid long first-boot times) |
| Need consistent environment across many launches | AMI |
| Dynamic config (specific to each instance, like instance ID) | User Data (can query metadata within the script) |
| Fast launch times required (Auto Scaling) | AMI (pre-installed = instant ready state) |

For Auto Scaling groups with fast scaling demands, prefer a custom AMI over user data scripts that take minutes to run — you want instances ready immediately.

---

## User Data in Auto Scaling Groups

In an ASG, every instance that launches runs the user data script on its first boot. This is how you configure instances dynamically at scale. However, for faster launch times and more reliable configuration, use a pre-baked AMI with a minimal user data script for truly instance-specific customization.

---

## Key Points / Exam Tips

- **Runs once, on first launch, as root** — default behavior
- **Running on every reboot requires explicit configuration** — non-default
- **User data cannot be used to query or run custom scripts at any time** — it only fires at launch
- **Instance metadata ≠ user data** — metadata is descriptive info (instance ID, IP, AMI ID), not execution
- **Size limit is 16 KB** — for large scripts, use a short user data script that pulls a larger script from S3

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Automate first-boot configuration" | EC2 User Data |
| "Install software when instance launches" | EC2 User Data |
| "Runs on every restart" | Non-default behavior — requires explicit configuration |
| "Query instance ID / IP from within the instance" | Instance Metadata Service (IMDS), not User Data |
| "Pre-install everything, fast launches" | Custom AMI (not User Data) |
