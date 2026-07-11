# MODULE 06/07 – Practice Lab: Advanced `journalctl` Commands
> **Hands-on Practice Lab – Working with Boot Logs, Time-Based Logs, Verbose Output, and Persistent Journal**

---

# Lab Objective

In this practice lab, you will learn how to:

- View boot-related journal logs.
- Display logs from previous boots.
- List all recorded boots.
- Search logs for a specific date and time.
- Display verbose journal output.
- Understand where journal logs are stored.
- Configure Persistent Journal Storage.
- Verify persistent journal logs after a reboot.

---

#  Introduction

As a Linux System Administrator, one of the most important troubleshooting skills is knowing **how to retrieve the correct logs quickly**.

The `journalctl` command provides many options to search logs by:

- Boot
- Date
- Time
- Service
- Priority
- Output Format

These options help administrators quickly identify the root cause of a problem.

---

# Lab 1 – View Current Boot Logs

To display all messages generated since the current boot:

```bash
journalctl -b
```

### Explanation

- `-b` = Boot

This command displays all journal messages generated after the system was last started.

This command is extremely useful after:

- System crashes
- Unexpected reboot
- Boot failures
- Kernel panic

---

# Lab 2 – List Available Boots

Display all recorded boots.

```bash
journalctl --list-boots
```

Example Output

```text
-1  8f76a2...
 0  94ab31...
```

### Explanation

| Boot ID | Meaning |
|----------|---------|
| `0` | Current Boot |
| `-1` | Previous Boot |
| `-2` | Two Boots Ago |

If only one boot appears:

```text
0
```

It usually means:

- Journal is not persistent
- Previous boot logs have been lost

---

# Lab 3 – Display Logs from a Specific Boot

Current boot:

```bash
journalctl -b 0
```

Previous boot:

```bash
journalctl -b -1
```

Two boots ago:

```bash
journalctl -b -2
```

This is useful when troubleshooting problems that occurred before the latest reboot.

---

# Lab 4 – Display Logs Between Specific Dates

Sometimes users report:

- "The server was slow yesterday."
- "The issue started at 8:30 AM."
- "The application failed between 2 PM and 3 PM."

Instead of searching every log, use time filters.

Example:

```bash
journalctl --since "2026-01-29"
```

Specific date and time:

```bash
journalctl --since "2026-01-29 08:30:00"
```

Specify both a start and end time:

```bash
journalctl \
--since "2026-01-29 08:30:00" \
--until "2026-01-30 08:30:00"
```

This displays only the logs generated within the specified time period.

---

# Lab 5 – Display Verbose Output

To display every available field stored inside the journal:

```bash
journalctl -o verbose
```

### Explanation

`-o`

means:

```text
Output Format
```

`verbose`

shows every field stored in each journal entry.

This is useful when detailed troubleshooting information is required.

---

# Lab 6 – Understand Where Journal Logs Are Stored

By default, Linux stores journal logs in:

```text
/run/log/journal
```

or

```text
/run/systemd/journal
```

These locations reside on a **temporary filesystem (tmpfs)**.

Verify using:

```bash
df -h
```

You will notice that `/run` is mounted as **tmpfs**.

Since tmpfs resides in RAM:

- Logs disappear after reboot.
- Previous boot logs are lost.

These are called:

> **Volatile Journal Logs**

---

# Lab 7 – Configure Persistent Journal Storage

To preserve journal logs after reboot, create a permanent journal directory.

Navigate to:

```bash
cd /var/log
```

Create the directory:

```bash
mkdir journal
```

---

# Step 2 – Set Ownership

Assign ownership to:

```bash
chown root:systemd-journal /var/log/journal
```

---

# Step 3 – Set Permissions

```bash
chmod 2755 /var/log/journal
```

### Explanation

Permission `2755` means:

- Owner = Read, Write, Execute
- Group = Read, Execute
- Others = Read, Execute
- SetGID Bit enabled

---

# Step 4 – Restart systemd-journald

Restart the service.

```bash
systemctl restart systemd-journald
```

Verify its status.

```bash
systemctl status systemd-journald
```

Expected:

```text
Active: active (running)
```

---

# Step 5 – Reboot the System

```bash
reboot
```

Wait until the system starts again.

---

# Step 6 – Verify Persistent Journal

After reboot:

```bash
journalctl --list-boots
```

Now multiple boots should appear.

Example:

```text
-1
 0
```

This confirms that journal logs are now persistent.

---

# Step 7 – Display Previous Boot Logs

Display current boot:

```bash
journalctl -b 0
```

Display previous boot:

```bash
journalctl -b -1
```

You should now see logs from before the reboot.

---

# Practice Exercises

---

## Exercise 1

Display current boot logs.

```bash
journalctl -b
```

---

## Exercise 2

List all recorded boots.

```bash
journalctl --list-boots
```

---

## Exercise 3

Display previous boot logs.

```bash
journalctl -b -1
```

---

## Exercise 4

Display logs since a specific date.

```bash
journalctl --since "2026-01-29"
```

---

## Exercise 5

Display logs between two dates.

```bash
journalctl \
--since "2026-01-29 08:30:00" \
--until "2026-01-30 08:30:00"
```

---

## Exercise 6

Display verbose output.

```bash
journalctl -o verbose
```

---

## Exercise 7

Create persistent journal storage.

```bash
mkdir /var/log/journal
```

---

## Exercise 8

Assign ownership.

```bash
chown root:systemd-journal /var/log/journal
```

---

## Exercise 9

Set permissions.

```bash
chmod 2755 /var/log/journal
```

---

## Exercise 10

Restart the journal service.

```bash
systemctl restart systemd-journald
```

---

## Exercise 11

Reboot the server.

```bash
reboot
```

---

## Exercise 12

Verify persistent journal.

```bash
journalctl --list-boots
```

---

# Troubleshooting Scenarios

### Scenario 1

The server crashed yesterday.

Command:

```bash
journalctl -b -1
```

---

### Scenario 2

A user reports a problem between 8:30 AM and 10:00 AM.

Command:

```bash
journalctl \
--since "2026-01-29 08:30:00" \
--until "2026-01-29 10:00:00"
```

---

### Scenario 3

You need detailed journal information.

Command:

```bash
journalctl -o verbose
```

---

### Scenario 4

You rebooted the server but previous logs disappeared.

Solution:

Configure Persistent Journal Storage.

---

# Quick Revision

| Command | Purpose |
|----------|---------|
| `journalctl -b` | Current boot logs |
| `journalctl -b -1` | Previous boot logs |
| `journalctl --list-boots` | List recorded boots |
| `journalctl --since` | Logs after a specific date/time |
| `journalctl --until` | Logs before a specific date/time |
| `journalctl -o verbose` | Verbose output |
| `mkdir /var/log/journal` | Create persistent journal directory |
| `chown root:systemd-journal /var/log/journal` | Set ownership |
| `chmod 2755 /var/log/journal` | Set permissions |
| `systemctl restart systemd-journald` | Restart journal service |

---

# Key Takeaways

- `journalctl` can search logs by **boot**, **date**, **time**, and **output format**.
- By default, journal logs are stored in **memory (RAM)** and disappear after a reboot.
- Creating `/var/log/journal` enables **persistent journal storage**.
- After enabling persistence, previous boot logs remain available even after the server reboots.
- Persistent journal logs are invaluable for troubleshooting system crashes and historical issues.

---

# Remember

> **Think of `journalctl` as a powerful search engine for Linux logs.**
>
> You can search logs by:
>
> - Boot
> - Date
> - Time
> - Service
> - Priority
> - Output Format
>
> Enabling **Persistent Journal Storage** ensures that important logs survive a reboot, making long-term troubleshooting much easier.