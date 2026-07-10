# MODULE 07/08 – Practice Lab: Logrotate, Log Analysis & Logger
> **Hands-on Practice Lab – Managing and Analyzing Linux Log Files**

---

# Lab Objective

In this practice lab, you will learn how to:

- Understand the purpose of Logrotate.
- Learn why log rotation is important.
- View and analyze Linux log files.
- Monitor logs in real-time.
- Understand the format of a log entry.
- Generate custom log messages using the `logger` command.
- Troubleshoot Linux systems using log files.

---

# Introduction

Linux systems continuously generate log files.

As time passes, these log files continue to grow larger.

If they are not managed properly:

- Disk space will become full.
- Performance may decrease.
- Troubleshooting becomes difficult.
- Applications may stop working if the filesystem becomes full.

To solve this problem, Linux provides a utility called **Logrotate**.

---

# What is Logrotate?

**Logrotate** is a Linux utility that automatically manages log files.

Its responsibilities include:

- Rotating log files
- Renaming old log files
- Creating new log files
- Compressing old logs
- Removing very old logs
- Preventing the `/var/log` filesystem from becoming full

---

# How Log Rotation Works

Suppose your current log file is:

```text
/var/log/messages
```

After rotation it may become:

```text
/var/log/messages-2026-07-10
```

or

```text
/var/log/messages.1
```

A brand new empty log file named:

```text
/var/log/messages
```

is automatically created.

Applications continue writing into the new file without interruption.

---

# Why Do We Need Log Rotation?

Without Logrotate:

- Log files keep growing forever.
- Disk space becomes full.
- Applications may fail.
- System performance decreases.

With Logrotate:

- Old logs are archived.
- New logs are created.
- Old logs are compressed.
- Very old logs are automatically deleted.

---

# Typical Log Rotation Process

```text
Current Log File
        │
        ▼
Logrotate Checks Schedule
        │
        ▼
Old Log Renamed
        │
        ▼
New Empty Log Created
        │
        ▼
Service Continues Writing Logs
        │
        ▼
Old Logs Compressed
        │
        ▼
Oldest Logs Deleted
```

---

# How Often Does Logrotate Run?

A Cron job executes Logrotate every day.

```text
Daily Cron Job
        │
        ▼
Checks Every Log File
        │
        ▼
Determines Whether Rotation Is Required
```

Although Logrotate checks daily:

- Some logs rotate daily.
- Some rotate weekly.
- Some rotate monthly.
- Some rotate when they reach a certain size.

---

# Log Retention

A common configuration is:

- Keep logs for 4 weeks.
- Compress older logs.
- Delete logs older than 4 weeks.

This helps conserve disk space.

---

# Lab 1 – View Log Files

Navigate to the log directory.

```bash
cd /var/log
```

Display its contents.

```bash
ls -l
```

Observe common log files such as:

- messages
- secure
- cron
- maillog
- boot.log

---

# Lab 2 – Analyze a Log File

View the last few entries from the general system log.

```bash
tail /var/log/messages
```

---

# Understanding a Log Entry

A typical log entry contains four parts.

```text
Timestamp    Hostname    Program    Message
```

Example:

```text
Jul 10 10:15:32 server1 sshd: Failed password for root
```

---

## Parts of the Log Entry

| Field | Description |
|---------|------------|
| Timestamp | Date and time the event occurred |
| Hostname | System that generated the log |
| Program | Application or service name |
| Message | Actual event or error |

---

# Lab 3 – Monitor Logs in Real-Time

To continuously monitor a log file:

```bash
tail -f /var/log/messages
```

Explanation:

- `tail`
- `-f` = Follow Mode

As new log entries are created, they immediately appear on the screen.

---

# Practice

Open a second terminal.

Generate activity on the server.

Observe how the log file updates automatically.

---

# Lab 4 – Monitor Authentication Logs

Authentication logs are stored in:

```text
/var/log/secure
```

Monitor them.

```bash
tail -f /var/log/secure
```

---

# Practice

Open another SSH session.

Attempt to log in using an incorrect password.

Example:

```text
ssh root@server
```

Enter the wrong password.

Observe the log entry appearing immediately inside:

```text
/var/log/secure
```

This demonstrates real-time monitoring.

---

# Lab 5 – View Boot Logs

Display boot messages.

```bash
tail /var/log/boot.log
```

Boot logs help troubleshoot:

- Boot failures
- Driver problems
- Service startup issues

---

# Lab 6 – Generate Log Messages Using Logger

Linux allows administrators to manually generate Syslog messages.

The command used is:

```bash
logger
```

---

## Send a Critical Message

```bash
logger -p user.crit "This is a Critical Test Message"
```

---

## Send a Warning Message

```bash
logger -p user.warning "This is a Warning Test Message"
```

---

## Monitor the Message

Open another terminal.

Run:

```bash
tail -f /var/log/messages
```

Execute the `logger` command.

Observe the custom message immediately appear inside the log file.

---

# Understanding the Logger Command

Syntax:

```bash
logger -p facility.priority "message"
```

Example:

```bash
logger -p user.err "Disk Space Low"
```

Where:

- Facility = user
- Priority = err
- Message = Disk Space Low

---

# Common Logger Priorities

| Priority | Description |
|-----------|-------------|
| emerg | Emergency |
| alert | Alert |
| crit | Critical |
| err | Error |
| warning | Warning |
| notice | Notice |
| info | Information |
| debug | Debug |

---

# Practice Exercises

---

## Exercise 1

View the log directory.

```bash
cd /var/log
ls -l
```

---

## Exercise 2

Display the last ten system messages.

```bash
tail -10 /var/log/messages
```

---

## Exercise 3

Monitor general logs.

```bash
tail -f /var/log/messages
```

---

## Exercise 4

Monitor authentication logs.

```bash
tail -f /var/log/secure
```

---

## Exercise 5

Attempt an incorrect SSH login.

Observe the authentication log.

---

## Exercise 6

Display boot logs.

```bash
tail /var/log/boot.log
```

---

## Exercise 7

Generate a warning message.

```bash
logger -p user.warning "Testing Warning Message"
```

---

## Exercise 8

Generate a critical message.

```bash
logger -p user.crit "Critical Server Test"
```

---

## Exercise 9

Monitor `/var/log/messages` while generating logger messages.

---

# Troubleshooting Scenarios

### Scenario 1

Users cannot log in.

Check:

```bash
tail -f /var/log/secure
```

---

### Scenario 2

The server failed to boot correctly.

Check:

```bash
tail /var/log/boot.log
```

---

### Scenario 3

Users report random application errors.

Check:

```bash
tail -f /var/log/messages
```

---

### Scenario 4

Test whether Syslog is functioning.

Generate a test message.

```bash
logger -p user.info "Testing Syslog"
```

Verify:

```bash
tail /var/log/messages
```

---

# Quick Revision

| Command | Purpose |
|----------|---------|
| `tail /var/log/messages` | View recent system logs |
| `tail -f /var/log/messages` | Monitor logs live |
| `tail -f /var/log/secure` | Monitor authentication logs |
| `tail /var/log/boot.log` | View boot logs |
| `logger` | Send custom Syslog messages |
| `logger -p user.warning` | Generate warning message |
| `logger -p user.crit` | Generate critical message |

---

# Key Takeaways

- **Logrotate** automatically manages growing log files.
- It rotates, compresses, archives, and deletes old logs.
- Linux log entries normally contain:
  - Timestamp
  - Hostname
  - Program
  - Message
- `tail -f` allows administrators to monitor logs in real time.
- The `logger` command is useful for testing Syslog and generating custom log messages.
- Analyzing logs is one of the first steps in troubleshooting Linux systems.

---

# Remember

> **A Linux System Administrator should always know where the log files are located before troubleshooting any issue.**
>
> The first question should always be:
>
> **"Which log file contains the information I need?"**
>
> Once you know the correct log file, tools like `tail`, `journalctl`, and `logger` become extremely powerful for identifying and resolving problems.