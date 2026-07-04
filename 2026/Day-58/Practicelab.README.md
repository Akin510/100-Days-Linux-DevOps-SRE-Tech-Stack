# Linux Practice Lab
# Module 06 - Systemd, Logging, Journalctl, Logrotate & Cron

> **NIT Academy**
>
> **Mastering Linux Bootcamp**
>
> **Estimated Time:** 2.5 – 3 Hours
>
> **Difficulty:** ⭐⭐⭐☆☆ (Intermediate)

---

# Lab Objectives

By the end of this lab, you should be able to:

- Explore the `/var` directory
- Understand Linux logging
- Explore `/var/log`
- Use `tail` for live log monitoring
- Generate custom log entries
- Explore `journalctl`
- Monitor logs in real time
- Understand the difference between `journalctl` and `rsyslog`
- Explore `logrotate`
- Understand retention policies
- Force log rotation
- Understand compressed logs
- Explore systemd timers
- Understand how Cron automates administration

---

# Lab 1 - Exploring the /var Directory

## Task 1

Display the contents of the `/var` directory.

```bash
ls /var
```

### Questions

1. What does `/var` stand for?
2. Which directory stores log files?
3. Which directory stores cached files?
4. Which directory stores application data?
5. Which directory stores cron spool files?

---

## Task 2

Check disk usage.

```bash
df -h
```

### Questions

1. Why is a full `/var` partition dangerous?
2. Which Linux services could stop working if `/var` becomes full?

---

# Lab 2 - Exploring Linux Log Files

Display all log files.

```bash
ls -lh /var/log
```

Locate the following files:

- messages
- secure
- cron
- maillog
- lastlog
- wtmp

### Questions

Which file normally stores:

- General system messages?
- Authentication events?
- Cron jobs?
- Mail logs?

---

# Lab 3 - Exploring rsyslog

Display the rsyslog configuration.

```bash
cat /etc/rsyslog.conf
```

Display additional configuration files.

```bash
ls /etc/rsyslog.d
```

### Questions

1. Why does Linux separate service configurations into `/etc/rsyslog.d`?
2. Which configuration file controls the global rsyslog configuration?

---

# Lab 4 - Watching Logs in Real Time

Open **Terminal 1**

```bash
tail -f /var/log/messages
```

Open **Terminal 2**

Generate a manual log entry.

```bash
logger "Student Lab - Testing Linux Logging"
```

Observe the output appearing in Terminal 1.

### Questions

1. Where did the message appear?
2. Which command created the log?
3. Why is `tail -f` useful for system administrators?

---

# Lab 5 - Exploring journalctl

Display all journal entries.

```bash
journalctl
```

Display only the last 20 entries.

```bash
journalctl -n 20
```

Display logs from the last minute.

```bash
journalctl --since "1 minute ago"
```

Display only SSH logs.

```bash
journalctl -u sshd
```

Display Cloudflared logs.

```bash
journalctl -u cloudflared -n 50 --no-pager
```

### Questions

1. What does `-u` represent?
2. What does `-n` represent?
3. Why is `--no-pager` useful?
4. What is a pager?

---

# Lab 6 - Watching journalctl in Real Time

Open **Terminal 1**

```bash
journalctl -f
```

Open **Terminal 2**

Generate another log entry.

```bash
logger "Testing journalctl live monitoring"
```

Observe the live output.

### Questions

1. What is the difference between `tail -f` and `journalctl -f`?
2. Which command would you use to monitor systemd services?

---

# Lab 7 - Exploring Logrotate

Display the global Logrotate configuration.

```bash
cat /etc/logrotate.conf
```

Locate the following directives:

- weekly
- rotate
- create
- dateext
- compress
- include

### Questions

Explain the purpose of each directive.

---

# Lab 8 - Exploring Service-Specific Logrotate Files

Display the service configurations.

```bash
ls -l /etc/logrotate.d
```

Open the rsyslog configuration.

```bash
cat /etc/logrotate.d/rsyslog
```

Open the chrony configuration.

```bash
cat /etc/logrotate.d/chrony
```

### Questions

1. Why does every application have its own Logrotate configuration?
2. Which configuration takes precedence?
   - `/etc/logrotate.conf`
   - `/etc/logrotate.d/*`

---

# Lab 9 - Understanding the Logrotate Timer

Display all systemd timers.

```bash
systemctl list-timers
```

Locate the Logrotate timer.

```bash
systemctl list-timers | grep logrotate
```

### Questions

1. What is the name of the timer?
2. Which service does it execute?
3. Why is Logrotate implemented as a timer instead of a cron job?

---

# Lab 10 - Exploring Logrotate Status

Display the Logrotate status database.

```bash
cat /var/lib/logrotate/logrotate.status
```

### Questions

1. Which log was rotated most recently?
2. Which log files are currently tracked?
3. Why does Linux maintain this status file?

---

# Lab 11 - Creating Your Own Log File

Create a custom application log.

```bash
cat << EOF >> /var/log/myapp.log
User Login
Dashboard Opened
Downloaded Report
User Logout
EOF
```

Verify the log.

```bash
cat /var/log/myapp.log
```

Display file information.

```bash
ls -lh /var/log/myapp.log
```

### Questions

1. Why do applications generate log files?
2. What is the purpose of the `logger` command versus manually creating a log file?

---

# Lab 12 - Testing Logrotate

Perform a dry run.

```bash
logrotate -d /etc/logrotate.conf
```

Force a log rotation.

```bash
logrotate -f /etc/logrotate.conf
```

Display the contents of `/var/log`.

```bash
ls -lh /var/log
```

### Questions

1. Was a new log file created?
2. Was the previous log renamed?
3. Did Logrotate display any warnings?

---

# Lab 13 - Compression

Locate compressed log files.

```bash
ls -lh /var/log
```

Look for files ending in:

```text
.gz
```

### Questions

1. Why are archived logs compressed?
2. Why is the original rotated file removed?
3. What are the advantages of compression?

---

# Lab 14 - Understanding Retention Policies

Review the following configuration.

```text
weekly
rotate 4
```

### Questions

1. How many weeks of logs are retained?
2. If changed to:

```text
daily
rotate 4
```

How many days would remain?

3. If changed to:

```text
monthly
rotate 6
```

How many months would remain?

---

# Lab 15 - Exploring Cron

List the daily cron jobs.

```bash
ls /etc/cron.daily
```

Display your current crontab.

```bash
crontab -l
```

Create a cron job that writes the current date to a file every minute.

Verify that the job executes successfully.

### Questions

1. Why is Cron useful?
2. What types of administrative tasks are commonly automated?

---

# RHCSA Challenge

Without using your notes, perform the following tasks:

- View only SSH logs.
- Display the last 50 journal entries.
- View logs generated during the last hour.
- Monitor logs in real time.
- Generate a custom log entry.
- Display the Logrotate configuration.
- Locate the Logrotate timer.
- Display the Logrotate status file.
- Force Logrotate to execute.
- Verify compressed log files.
- Explain the retention policy.

---

# Enterprise Challenge

You are the Linux Administrator for your company.

A developer reports that the application has stopped writing logs.

Investigate the problem using Linux commands.

Perform the following:

1. Check disk usage.
2. Verify that `/var` is not full.
3. Check `/var/log`.
4. Review journal entries.
5. Verify the rsyslog configuration.
6. Check the Logrotate configuration.
7. Verify that the Logrotate timer is active.
8. Determine whether the logs were rotated successfully.
9. Recommend a permanent solution.

---

# Reflection Questions

1. Explain the difference between `journalctl` and `rsyslog`.
2. Explain the difference between `journalctl -f` and `tail -f`.
3. Why does Linux rotate logs?
4. Why is `/var` considered a critical filesystem?
5. What would happen if Logrotate stopped working?
6. Why are logs valuable during troubleshooting?
7. How do enterprise organizations centralize logs?
8. Why do companies keep historical log archives?

---

# Congratulations!

You have completed **Module 06 Practical Lab**.

You have practiced:

- Systemd
- Linux Logging
- `/var`
- rsyslog
- journalctl
- Logrotate
- Compression
- Retention Policies
- Systemd Timers
- Cron
- Enterprise Troubleshooting

These skills closely match the daily responsibilities of a **Linux System Administrator**, **RHCSA**, **DevOps Engineer**, **SRE**, and **Platform Engineer**.

---

**NIT Academy**

**Mastering Linux Bootcamp**