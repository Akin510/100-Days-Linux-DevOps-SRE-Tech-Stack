# Module 06 - Linux System Administration
# DAY-58
# Systemd, Logging, `/var`, Journalctl, Rsyslog, Logrotate, Backup & Cron

> JULY 4TH, 2026
> **NIT Academy**
>
> Mastering Linux Bootcamp
>
> Module 06 Learning Notes

---

# Learning Objectives

After completing this module, students should be able to:

- Understand Systemd and PID 1
- Explain Linux logging architecture
- Understand the `/var` directory
- Use `journalctl`
- Use `rsyslog`
- Understand Linux logs
- Configure and troubleshoot `logrotate`
- Understand retention policies
- Explain why organizations archive logs
- Understand Cron and automation

---

# Chapter 1 - Systemd

## What is Systemd?

Systemd is the **System and Service Manager** used by modern Linux distributions.

It is responsible for:

- Starting Linux
- Managing services
- Managing timers
- Managing devices
- Mounting filesystems
- Starting targets
- Boot management

Systemd is the **first userspace process** started by Linux.

```text
PID 1
```

Everything running on Linux ultimately starts because Systemd starts it.

---

# Linux Boot Sequence

```text
BIOS / UEFI
      │
      ▼
Boot Loader (GRUB)
      │
      ▼
Linux Kernel
      │
      ▼
systemd (PID 1)
      │
 ┌────┼─────────────┐
 ▼    ▼             ▼
sshd  Network     Cron
Apache
MariaDB
Docker
Cloudflared
```

---

# Systemd Units

Everything managed by Systemd is called a **Unit**.

Common unit types include:

| Unit | Purpose |
|-------|----------|
| Service | Runs services |
| Target | Collection of units |
| Timer | Scheduled jobs |
| Mount | Filesystem mounts |
| Device | Hardware devices |
| Socket | Network sockets |

Examples:

```text
sshd.service
docker.service
NetworkManager.service
logrotate.timer
multi-user.target
```

---

# systemctl

System administrators use:

```bash
systemctl
```

to manage services.

## Examples

Start a service

```bash
systemctl start httpd
```

Stop a service

```bash
systemctl stop httpd
```

Restart

```bash
systemctl restart httpd
```

Reload configuration

```bash
systemctl reload httpd
```

Enable at boot

```bash
systemctl enable httpd
```

Disable

```bash
systemctl disable httpd
```

View status

```bash
systemctl status httpd
```

---

# Important Systemd Directories

| Directory | Purpose |
|------------|----------|
| `/usr/lib/systemd/system/` | Vendor supplied unit files |
| `/run/systemd/system/` | Runtime unit files |
| `/etc/systemd/system/` | Administrator customizations |

Priority:

```text
Highest
│
├── /etc/systemd/system
│
├── /run/systemd/system
│
└── /usr/lib/systemd/system
Lowest
```

---

# Chapter 2 - Understanding `/var`

`/var` stands for:

> Variable

It stores files that continuously change while Linux is running.

Examples include:

- logs
- mail
- caches
- print queues
- databases
- package metadata
- temporary files

---

# Important `/var` Directories

| Directory | Purpose |
|------------|----------|
| `/var/log` | Log files |
| `/var/lib` | Application state |
| `/var/cache` | Cached data |
| `/var/tmp` | Temporary files |
| `/var/spool` | Waiting jobs |

---

# Why `/var` Matters

Most Linux services write data into `/var`.

Examples:

Apache

```text
/var/log/httpd/
```

MariaDB

```text
/var/lib/mysql/
```

DNF

```text
/var/cache/dnf/
```

Cron

```text
/var/spool/
```

---

# Why Monitor `/var`?

If `/var` becomes full:

- logging stops
- databases fail
- package installations fail
- web servers stop
- services crash

Always monitor disk usage:

```bash
df -h
```

---

# Chapter 3 - Linux Logging

Linux constantly records events called **logs**.

Examples:

- user logins
- service failures
- kernel messages
- cron jobs
- firewall events
- software installations

Logs answer:

- What happened?
- When?
- Who?
- Why?

---

# Linux Logging Architecture

```text
Applications
Kernel
Services
      │
      ▼
systemd-journald
      │
      ▼
rsyslog
      │
      ▼
/var/log
```

---

# systemd-journald

Systemd-journald:

- Collects logs
- Stores logs in binary format
- Supports advanced searching

Think of it as:

> The Collector

---

# journalctl

`journalctl` reads the Systemd journal database.

Useful commands:

View all logs

```bash
journalctl
```

Last 20 entries

```bash
journalctl -n 20
```

Follow live logs

```bash
journalctl -f
```

Last hour

```bash
journalctl --since "1 hour ago"
```

SSH logs

```bash
journalctl -u sshd
```

Cloudflared logs

```bash
journalctl -u cloudflared
```

Without pager

```bash
journalctl -u cloudflared -n 50 --no-pager
```

---

# What is a Pager?

Normally journalctl opens output inside:

```text
less
```

This allows scrolling.

Using:

```bash
--no-pager
```

prints everything directly to the terminal.

Useful for:

- scripting
- automation
- copying logs

---

# journalctl Live Monitoring

Terminal 1

```bash
journalctl -f
```

Terminal 2

```bash
logger "Linux Logging Test"
```

Watch the new log appear immediately.

---

# rsyslog

Think of rsyslog as:

> The Text Writer

It writes logs into:

```text
/var/log/messages
/var/log/secure
/var/log/cron
/var/log/maillog
```

Configuration:

```text
/etc/rsyslog.conf
```

Additional configuration:

```text
/etc/rsyslog.d/
```

---

# Useful Commands

Watch logs

```bash
tail -f /var/log/messages
```

Generate a test log

```bash
logger "Hello Linux"
```

---

# journalctl vs rsyslog

| journalctl | rsyslog |
|------------|----------|
| Binary database | Plain text |
| Fast searching | Sequential searching |
| Structured | Simple files |
| Service filtering | Text filtering |

---

# Chapter 4 - SIEM

SIEM stands for:

```text
Security Information and Event Management
```

A SIEM collects logs from many servers into one central platform.

Functions include:

- Centralized logging
- Correlation
- Real-time alerts
- Compliance
- Long-term retention

Popular SIEMs

| Product | Type |
|----------|------|
| Splunk | Enterprise |
| Elastic (ELK) | Open Source |
| Microsoft Sentinel | Cloud |
| Wazuh | Open Source |

---

# Chapter 5 - Logrotate

## Why Logrotate?

Logs never stop growing.

Without logrotate:

```text
messages

↓

10 GB

↓

100 GB

↓

500 GB

↓

Disk Full
```

---

# What Does Logrotate Do?

Automatically:

- Rotates logs
- Compresses logs
- Creates new log files
- Deletes old archives

---

# Configuration Files

Global configuration

```text
/etc/logrotate.conf
```

Service configuration

```text
/etc/logrotate.d/
```

---

# Example Configuration

```text
weekly

rotate 4

create

dateext

compress

include /etc/logrotate.d
```

---

# Understanding Each Directive

## weekly

Rotate once every week.

---

## rotate 4

Keep four archives.

---

## create

Create a new empty log after rotation.

---

## dateext

Append dates to rotated logs.

---

## compress

Compress archived logs.

---

# Retention Policy

Example

```text
weekly

rotate 4
```

Produces

```text
Week 1

app.log.1

Week 2

app.log.1
app.log.2

Week 3

app.log.1
app.log.2
app.log.3

Week 4

app.log.1
app.log.2
app.log.3
app.log.4

Week 5

Delete app.log.4
Create new archive
```

Only four weeks remain.

---

# Why Delete the Original Log?

After rotation:

```text
messages

↓

messages-20260701

↓

messages-20260701.gz
```

The original rotated log is deleted because:

- compression already contains the data
- saves disk space
- prevents duplicate copies

---

# Logrotate Timer

View timers

```bash
systemctl list-timers
```

Look for:

```text
logrotate.timer
```

---

# Logrotate Status

```bash
cat /var/lib/logrotate/logrotate.status
```

Shows:

- last rotation
- timestamps
- tracked logs

---

# Practice Log

```bash
cat << EOF >> /var/log/myapp.log

User Login

User Logout

EOF
```

---

# Test Logrotate

Dry run

```bash
logrotate -d /etc/logrotate.conf
```

Force rotation

```bash
logrotate -f /etc/logrotate.conf
```

---

# Chapter 6 - Backup Concepts

Organizations archive logs because they are valuable.

Typical workflow:

```text
Applications

↓

Logs

↓

Logrotate

↓

Compressed Archives

↓

Backup Server

↓

Long-Term Storage
```

---

# Why Use NFS?

Many organizations copy archived logs to an NFS server.

Example:

```text
Production Server

↓

Logrotate

↓

Compressed Logs

↓

/backup

↓

NFS Mount

↓

Backup Server
```

Benefits:

- Saves disk space
- Centralized backups
- Easier disaster recovery
- Long-term retention

---

# Chapter 7 - Cron

Cron automatically executes commands on a schedule.

Common tasks:

- backups
- logrotate
- cleanup
- reports
- maintenance scripts

Useful commands

Edit cron

```bash
crontab -e
```

List cron jobs

```bash
crontab -l
```

System-wide jobs

```bash
ls /etc/cron.daily
```

---

# Production Logging Workflow

```text
Applications
      │
      ▼
systemd-journald
      │
      ▼
rsyslog
      │
      ▼
/var/log
      │
      ▼
logrotate
      │
      ▼
Compressed Archives
      │
      ▼
NFS / NAS / Cloud Backup
      │
      ▼
SIEM
      │
      ▼
Security Monitoring
```

---

# Key Takeaways

- Systemd (PID 1) starts Linux services.
- `/var` stores variable system data.
- `systemd-journald` collects logs.
- `journalctl` reads binary logs.
- `rsyslog` writes traditional text logs.
- `logrotate` prevents disks from filling.
- Retention policies determine how many archived logs remain.
- Organizations archive logs for compliance and recovery.
- Cron automates repetitive administrative tasks.
- Enterprise environments forward logs into SIEM platforms for centralized monitoring.

---

# End of Module 06

**Mastering Linux**

**NIT Academy**