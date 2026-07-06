# Linux Practice Lab – Managing Services with systemctl (RHCSA Style)
# Day-60
> JULY 06, 2026
## Objective

After completing this lab, you will be able to:

- Understand the purpose of systemd
- View service status
- Read systemctl status output
- Locate unit configuration files
- Start and stop services
- Enable and disable services
- Understand symbolic links created by enable
- Restart and reload services
- Compare restart vs reload
- Mask and unmask services
- Understand how Linux boots into services

---

# Lab Environment

- Operating System: Rocky Linux 9
- User: root (or sudo user)

---

# Scenario

You have recently joined a company as a Linux System Administrator.

One of your responsibilities is managing Linux services using **systemd**.

In this lab you will inspect, start, stop, restart, reload, enable, disable, mask, and unmask services exactly as a Linux administrator would in production.

---

# Task 1 – Read the systemctl Manual

Open the manual page.

```bash
man systemctl
```

Search inside the manual.

```
/restart
```

Move forward

```
n
```

Move backward

```
N
```

Quit the manual.

```
q
```

---

# Task 2 – Check the Status of sshd

Run

```bash
systemctl status sshd.service
```

Example

```
● sshd.service - OpenSSH server daemon

Loaded: loaded (...)

Active: active (running)

Main PID: 770

Tasks: 1

Memory: 8 MB

CPU: 14 seconds
```

---

# Understanding the Output

## Loaded

Example

```
Loaded:
loaded (/usr/lib/systemd/system/sshd.service; enabled)
```

Meaning

The unit configuration file has been loaded.

Notice the location

```
/usr/lib/systemd/system/
```

This is where most system supplied unit files are stored.

---

## Active

```
Active: active (running)
```

Meaning

The service is currently running.

---

## Enabled

```
enabled
```

Meaning

The service will automatically start during system boot.

---

## Main PID

```
Main PID: 770
```

Meaning

This is the Process ID of the running service.

---

## Tasks

Shows how many processes or threads belong to this service.

---

## Memory

Displays RAM currently being used.

---

## CPU

Displays total CPU time consumed since the service started.

---

# Task 3 – View Another Service

Check the rsyslog service.

```bash
systemctl status rsyslog
```

Observe

- Loaded
- Active
- PID
- Memory
- CPU
- Logs

Notice the log messages displayed at the bottom.

These are recent journal entries related to this service.

---

# Task 4 – Locate Unit Files

Move into the systemd unit directory.

```bash
cd /usr/lib/systemd/system
```

Display the files.

```bash
ls -ltr
```

Notice different unit types.

Example

```
logrotate.service

logrotate.timer

dnf-makecache.service

dnf-makecache.timer

sshd.service

multi-user.target

graphical.target
```

---

# Understanding Unit Types

| Unit | Purpose |
|--------|----------|
| .service | Service |
| .timer | Scheduler |
| .target | Boot Target |
| .socket | Socket Activation |
| .mount | Mount Point |
| .device | Device |
| .path | Monitor File Changes |

---

# Task 5 – Stop a Service

Stop rsyslog.

```bash
systemctl stop rsyslog
```

Verify.

```bash
systemctl status rsyslog
```

Expected

```
Active: inactive (dead)
```

Question

Did the configuration file disappear?

Answer

No.

Only the running process stopped.

---

# Task 6 – Start the Service Again

```bash
systemctl start rsyslog
```

Verify.

```bash
systemctl status rsyslog
```

Expected

```
Active: active (running)
```

---

# Task 7 – Disable a Service

Disable rsyslog.

```bash
systemctl disable rsyslog
```

Output

```
Removed

/etc/systemd/system/multi-user.target.wants/rsyslog.service
```

---

# What Happened?

Linux removed a symbolic link.

The service still exists.

However, Linux will no longer start this service automatically during boot.

---

# Verify

```bash
systemctl status rsyslog
```

Notice

```
disabled
```

---

# Task 8 – Enable the Service Again

Enable rsyslog.

```bash
systemctl enable rsyslog
```

Output

```
Created symlink

/etc/systemd/system/multi-user.target.wants/rsyslog.service
```

Verify.

```bash
systemctl status rsyslog
```

Notice

```
enabled
```

---

# Task 9 – Inspect the Symbolic Link

List the symbolic links.

```bash
ls -l /etc/systemd/system/multi-user.target.wants/
```

Locate

```
rsyslog.service
```

Question

Where does the symbolic link point?

---

# Task 10 – Verify the Linux init Process

Display init.

```bash
ls -l /sbin/init
```

Output

```
/sbin/init -> ../lib/systemd/systemd
```

Question

What does this tell us?

Answer

systemd is PID 1 and is the first userspace process started by the Linux kernel.

---

# Task 11 – Restart a Service

Check the PID.

```bash
systemctl status rsyslog
```

Example

```
Main PID: 81322
```

Restart.

```bash
systemctl restart rsyslog
```

Check again.

```bash
systemctl status rsyslog
```

Example

```
Main PID: 81331
```

Question

Did the PID change?

Answer

Yes.

Restart stops the service and starts it again.

---

# Task 12 – Reload a Service

Reload the configuration.

```bash
systemctl reload rsyslog
```

Check status.

```bash
systemctl status rsyslog
```

Notice

```
ExecReload

Main PID: 81331
```

Question

Did the PID change?

Answer

No.

Reload simply re-reads the configuration without stopping the service.

---

# Restart vs Reload

| Restart | Reload |
|----------|---------|
| Stops service | Does not stop service |
| Starts service again | Reads configuration again |
| PID changes | PID stays the same |
| Small interruption | Usually no interruption |

---

# Task 13 – Reload or Restart

Run

```bash
systemctl reload-or-restart rsyslog
```

Purpose

If reload is supported

→ Reload

Otherwise

→ Restart

This is useful when you are unsure whether a service supports reload.

---

# Task 14 – Mask a Service

Stop the service.

```bash
systemctl stop rsyslog
```

Mask it.

```bash
systemctl mask rsyslog
```

Output

```
Created symlink

/etc/systemd/system/rsyslog.service

->

/dev/null
```

---

# What Does Mask Mean?

Masking completely prevents the service from starting.

Even root cannot accidentally start it.

---

# Verify

```bash
systemctl status rsyslog
```

Notice

```
Loaded:

masked
```

---

# Task 15 – Attempt to Start the Service

```bash
systemctl start rsyslog
```

Expected

```
Failed

Unit is masked.
```

---

# Task 16 – Unmask the Service

```bash
systemctl unmask rsyslog
```

Output

```
Removed

/etc/systemd/system/rsyslog.service
```

Now start it.

```bash
systemctl start rsyslog
```

Verify.

```bash
systemctl status rsyslog
```

Expected

```
Active:

active (running)
```

---

# Summary Table

| Command | Purpose |
|----------|----------|
| systemctl status | Show service status |
| systemctl start | Start service |
| systemctl stop | Stop service |
| systemctl restart | Restart service |
| systemctl reload | Reload configuration |
| systemctl reload-or-restart | Reload if supported otherwise restart |
| systemctl enable | Start automatically during boot |
| systemctl disable | Do not start during boot |
| systemctl mask | Completely block the service |
| systemctl unmask | Remove the block |

---

# RHCSA Challenge 1

Check the status of

```
sshd

firewalld

chronyd
```

Write down

- Active
- PID
- Memory
- CPU

---

# RHCSA Challenge 2

Find three timer units inside

```
/usr/lib/systemd/system
```

---

# RHCSA Challenge 3

Disable the rsyslog service.

Verify its status.

Enable it again.

---

# RHCSA Challenge 4

Restart rsyslog.

Record the PID.

Restart it again.

Did the PID change?

---

# RHCSA Challenge 5

Reload rsyslog.

Did the PID change?

Explain why.

---

# RHCSA Challenge 6

Mask rsyslog.

Attempt to start it.

Record the error.

---

# RHCSA Challenge 7

Unmask rsyslog.

Start it successfully.

---

# Knowledge Check

1. Where are most system unit files stored?

2. What does **enabled** mean?

3. What does **disabled** mean?

4. What does **masked** mean?

5. Which command starts a service?

6. Which command stops a service?

7. Which command restarts a service?

8. Which command reloads configuration files?

9. Which command starts a service automatically during boot?

10. Which command prevents a service from ever starting?

11. Which process is PID 1?

12. What does `/sbin/init` point to on Rocky Linux 9?

---

# Summary

After completing this lab you should be able to:

✅ Read systemctl status output

✅ Locate unit configuration files

✅ Start and stop services

✅ Enable and disable services

✅ Understand symbolic links created by enable

✅ Restart services

✅ Reload configuration without changing the PID

✅ Compare restart vs reload

✅ Mask and unmask services

✅ Explain how systemd manages Linux services

These are core Linux administration skills and are heavily tested in RHCSA exams and used daily by Linux System Administrators in production environments.