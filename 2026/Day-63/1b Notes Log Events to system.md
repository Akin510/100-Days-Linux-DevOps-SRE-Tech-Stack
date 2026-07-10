# MODULE 06/07 – Rsyslog Configuration & Logging Rules
> **Logging Events to the System**

---

# 📖 Introduction

In the previous lesson, we learned that Linux uses two main services for logging:

- **systemd-journald**
- **rsyslog**

In this lesson, we will learn:

- How logs are categorized
- How `rsyslog` decides where logs are stored
- Facilities
- Priorities (Severity Levels)
- Rsyslog configuration syntax
- Common examples

---

# 1. Logging Events to the System

Many Linux programs use the **Syslog Protocol** to record events on the system.

Every log message contains **two important pieces of information**:

1. **Facility**
2. **Priority (Severity)**

These two values help `rsyslog` decide where the message should be stored.

---

# 2. What is a Facility?

A **Facility** identifies **which subsystem or service generated the log message**.

Think of it as the **category** of the log.

Examples include:

- Kernel messages
- Cron jobs
- Mail server
- Security authentication
- User applications
- Printing system

Each facility can be written to a different log file.

---

# 3. Examples of Log Files

Linux stores different types of logs in separate files.

| Facility | Log File |
|-----------|----------|
| Authentication | `/var/log/secure` |
| Cron Jobs | `/var/log/cron` |
| Mail | `/var/log/maillog` |
| Boot Process | `/var/log/boot.log` |
| General Messages | `/var/log/messages` |

This organization makes troubleshooting much easier.

---

# 4. How Does Linux Know Where to Store Logs?

The **rsyslog** service uses configuration files to decide:

- Which messages to capture
- Which facility they belong to
- Which priority they have
- Which log file they should be written to

These rules are stored inside the rsyslog configuration.

Common configuration files include:

```text
/etc/rsyslog.conf
```

and

```text
/etc/rsyslog.d/
```

---

# 5. Rsyslog Rule Syntax

Every rsyslog rule follows the same format.

```text
facility.priority    destination
```

Where:

- **facility** = source of the message
- **priority** = severity level
- **destination** = log file or remote server

Example:

```text
authpriv.*    /var/log/secure
```

This means:

- Facility = Authentication
- Priority = All
- Store logs in `/var/log/secure`

---

# 6. Common Facilities

Below are some commonly used facilities.

| Facility | Purpose |
|-----------|---------|
| `kern` | Kernel messages |
| `user` | User-level messages |
| `mail` | Mail server messages |
| `daemon` | System daemon messages |
| `auth` | Authentication messages |
| `authpriv` | Private authentication messages |
| `cron` | Cron scheduler |
| `syslog` | Syslog internal messages |
| `local0-local7` | Custom application logging |

---

# 7. Priority (Severity Levels)

Every log message also has a severity level.

The lower the level, the more critical the event.

| Priority | Description |
|-----------|-------------|
| `emerg` | System is unusable |
| `alert` | Immediate action required |
| `crit` | Critical condition |
| `err` | Error |
| `warning` | Warning |
| `notice` | Normal but significant |
| `info` | Informational message |
| `debug` | Debugging information |

---

# 8. Wildcard (*)

The asterisk (`*`) is a **wildcard**.

It means:

> **Match every possible value.**

Example:

```text
authpriv.*
```

Meaning:

- Authentication facility
- Every priority level

---

# 9. Example 1

```text
authpriv.*    /var/log/secure
```

Explanation:

| Part | Meaning |
|------|---------|
| `authpriv` | Authentication facility |
| `*` | All priority levels |
| `/var/log/secure` | Destination log file |

This rule stores **all authentication messages** inside:

```text
/var/log/secure
```

---

# 10. Example 2

```text
*.info;mail.none;authpriv.none;cron.none    /var/log/messages
```

This example is commonly found in Red Hat systems.

Let's break it down.

---

## Part 1

```text
*.info
```

Meaning:

- Every facility
- Information level and higher

---

## Part 2

```text
mail.none
```

Meaning:

Do **not** include mail messages.

---

## Part 3

```text
authpriv.none
```

Meaning:

Do **not** include authentication messages.

---

## Part 4

```text
cron.none
```

Meaning:

Do **not** include cron messages.

---

## Destination

```text
/var/log/messages
```

All remaining messages are written here.

---

# 11. What Does This Rule Mean?

```text
*.info;mail.none;authpriv.none;cron.none    /var/log/messages
```

In simple English:

Store **all informational messages and higher** from every facility **except**:

- Mail
- Authentication
- Cron

inside:

```text
/var/log/messages
```

---

# 12. The Keyword "none"

The keyword:

```text
none
```

means:

> **Exclude this facility.**

Example:

```text
mail.none
```

means

> Ignore mail logs.

---

# 13. Logging Flow

```text
Application
      │
      ▼
Generates Log
      │
      ▼
Facility Assigned
      │
      ▼
Priority Assigned
      │
      ▼
rsyslog Reads Rule
      │
      ▼
Correct Log File
```

---

# 14. Why Are Facilities Important?

Facilities allow Linux to separate different kinds of logs.

Instead of placing everything into one huge file:

- Authentication logs go to one file.
- Cron logs go to another.
- Mail logs go somewhere else.
- Kernel logs go to their own location.

This makes troubleshooting much easier.

---

# 15. Why Are Priorities Important?

Priorities help administrators identify:

- Critical failures
- Errors
- Warnings
- Informational messages
- Debug messages

Without priorities, every message would look equally important.

---

# 16. Quick Revision

| Symbol | Meaning |
|---------|---------|
| `*` | Match all values |
| `.none` | Exclude this facility |
| `.info` | Information level and higher |
| `.debug` | Debug messages |
| `.err` | Error messages |

---

# 17. Important Configuration Files

| File | Purpose |
|------|---------|
| `/etc/rsyslog.conf` | Main rsyslog configuration |
| `/etc/rsyslog.d/` | Additional configuration files |
| `/var/log/messages` | General system logs |
| `/var/log/secure` | Authentication logs |
| `/var/log/cron` | Cron logs |
| `/var/log/maillog` | Mail logs |
| `/var/log/boot.log` | Boot logs |

---

# 18. Interview Questions

### Question 1

What are the two parts of every syslog message?

**Answer**

- Facility
- Priority (Severity)

---

### Question 2

What is a Facility?

**Answer**

The subsystem or service that generated the log message.

---

### Question 3

What is Priority?

**Answer**

The severity level of the log message.

---

### Question 4

What does the wildcard `*` mean?

**Answer**

Match every possible value.

---

### Question 5

What does `.none` mean?

**Answer**

Exclude that facility.

---

### Question 6

Where are authentication logs normally stored?

**Answer**

```text
/var/log/secure
```

---

### Question 7

Where are cron logs stored?

**Answer**

```text
/var/log/cron
```

---

### Question 8

Where is the main rsyslog configuration file?

**Answer**

```text
/etc/rsyslog.conf
```

---

# 19. Key Takeaways

- Every log message has a **Facility** and a **Priority**.
- The **Facility** identifies where the message originated.
- The **Priority** identifies how serious the event is.
- **rsyslog** reads its configuration rules to decide where each log message should be stored.
- Using facilities and priorities keeps system logs organized and makes troubleshooting much easier.

---

# Remember

> **Linux logging is like sorting mail in a post office.**
>
> - The **Facility** tells you **who sent the message**.
> - The **Priority** tells you **how urgent the message is**.
> - **rsyslog** decides **which mailbox (log file) the message belongs in**.