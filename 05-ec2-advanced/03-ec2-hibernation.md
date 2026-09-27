# EC2 Hibernation

## What Is EC2 Hibernation?

**EC2 Hibernation** is a feature that saves the entire in-memory (RAM) state of an instance to the EBS root volume, then stops the instance. When you start it again, the RAM state is restored — the instance **resumes exactly where it left off** rather than going through a full reboot and application initialization sequence.

This is the same concept as "suspend to disk" (hibernate mode) on a laptop.

---

## How Hibernation Works

### Hibernating (stopping)

```
Running Instance (RAM state: "application running, connections open, caches warm")
         ↓ Hibernate command
RAM contents written to encrypted EBS root volume
EC2 instance stops → you stop paying for compute
```

### Resuming

```
Hibernate command (start)
         ↓
EBS root volume restored to previous state
RAM contents reloaded from EBS
Processes resume exactly where they were (same state, same connections)
         ↓
Application is immediately back at its previous state
```

**Contrast with a normal Stop/Start**: On a normal start, the OS boots from scratch, init systems run, services start, application initializes — this can take minutes. With hibernation, you skip all of that.

---

## Requirements and Constraints

| Requirement | Detail |
|---|---|
| **EBS root volume** | Must be EBS-backed (instance store-backed instances cannot hibernate) |
| **Root volume encryption** | Must be **enabled** — the RAM dump contains sensitive data |
| **RAM size** | Must be less than 150 GB |
| **Instance type** | Must be in a supported instance family (most general purpose, compute, memory-optimized) |
| **OS** | Amazon Linux 2/2023, Ubuntu, Windows (check AWS docs for current list) |
| **Maximum hibernation duration** | Instance can remain hibernated for up to **60 days** |
| **Instance age** | The instance must not have been hibernated for more than 60 days |

---

## Use Cases

### 1. Long-Running Applications That Are Expensive to Re-Initialize

Analytics workloads, machine learning jobs, or applications that take a long time to load a large dataset into memory. Instead of re-loading data from disk every time:
- Hibernate when not needed (nights/weekends)
- Resume and the application picks up instantly — data already in RAM

### 2. Development Workstations

Developers hibernate their EC2-based dev environment at end of day, resume in the morning with all their work exactly as they left it — open files, running services, etc.

### 3. Cost Optimization with State Preservation

For workloads that need RAM state but only run during business hours, hibernation avoids both the cost of a running instance AND the cost of re-initialization time.

---

## Hibernation vs Stop vs Reboot

| | Hibernation | Stop (normal) | Reboot |
|---|---|---|---|
| RAM state preserved | Yes | No | No |
| EBS data preserved | Yes | Yes | Yes |
| OS boot on resume | No (resume from hibernate) | Yes (full boot) | Yes (full restart) |
| Application init on resume | No | Yes | Yes |
| Instance ID changes | No | No | No |
| Private IP changes | No | No | No |
| Public IP changes | No (on resume) | Yes (new public IP) | No |
| Compute charge during | No | No | Yes |

---

## Hibernation vs User Data / AMI for Startup Speed

This is an exam differentiation:

| Approach | What It Does | Faster Startup? |
|---|---|---|
| **Hibernation** | Saves RAM state; resumes without booting or re-initializing | Yes — no init sequence at all |
| **Custom AMI** | Pre-installs software so boot is faster; still has to boot | Somewhat faster boot, but still boots fully |
| **User Data** | Automates setup commands at boot | No — may slow boot if there are lots of packages to install |
| **EC2 Metadata** | Info about the instance, not related to boot speed | Not relevant |

**Key exam insight**: The only way to resume exactly from a prior RAM state (avoiding initialization) is **Hibernation**. AMIs, User Data, and Metadata cannot preserve RAM state.

---

## Key Points / Exam Tips

- **Root volume must be encrypted** for hibernation to be allowed
- **Instance Store-backed instances cannot hibernate** (no EBS to write RAM to)
- **Maximum hibernation duration: 60 days**
- **RAM limit: 150 GB**
- **Resumes from exactly where it left off** — no OS boot, no app initialization
- **Saves money + preserves state** — key value proposition

## Trigger Words

| Exam phrase | Think |
|---|---|
| "Resume application without re-initialization" | EC2 Hibernation |
| "Speed up restart of slow-starting application" | EC2 Hibernation (not AMI, User Data, or Metadata) |
| "Preserve in-memory state across stop/start" | EC2 Hibernation |
| "Analytics workload with pre-loaded data in RAM" | Hibernate when not in use, resume instantly |
| "Instance hibernated for >60 days" | Not allowed — 60-day maximum |
| "Hibernation requires encrypted root volume" | Correct — mandatory requirement |
