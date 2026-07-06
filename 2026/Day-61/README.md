# Linux Practice Lab – Understanding systemd Targets (RHCSA Style)
# DAY-61
> DATE JULY 08, 2026
## Objective

After completing this lab, you will be able to:

- Understand what a systemd target is
- Understand the relationship between targets and traditional runlevels
- Identify the system's default boot target
- Locate target unit files
- Explore common systemd targets
- List target dependencies
- Interpret the output of `systemctl list-dependencies`
- Understand the meaning of active, inactive, and failed units

---

# Lab Environment

- Operating System: Rocky Linux 9
- User: root (or sudo user)

---

# Scenario

As a Linux System Administrator, you are responsible for understanding how Linux boots.

Modern Linux distributions use **systemd targets** instead of traditional runlevels.

In this lab, you will learn how Linux determines its default boot mode, where target files are stored, and how different targets control which services start during boot.

---

# Background

Older Linux distributions such as **RHEL 5** and **RHEL 6** used **runlevels**.

Modern Linux distributions such as **Rocky Linux 9**, **RHEL 9**, and **CentOS Stream** use **systemd targets**.

Think of a **target** as the modern replacement for a **runlevel**.

---

# Common Target Mapping

| Old Runlevel | systemd Target | Purpose |
|--------------|----------------|---------|
| 0 | poweroff.target | Shut down the system |
| 1 | rescue.target | Single-user rescue mode |
| 3 | multi-user.target | Multi-user text mode |
| 5 | graphical.target | Graphical desktop mode |
| 6 | reboot.target | Restart the system |

---

# Task 1 – Determine the Default Boot Target

Navigate to the systemd configuration directory.

```bash
cd /etc/systemd/system
```

Display the default target.

```bash
ls -l default.target
```

Example Output

```text
default.target -> /usr/lib/systemd/system/multi-user.target
```

---

# Discussion

Notice that **default.target** is not a regular file.

It is a **symbolic link**.

Question:

What target is your system configured to boot into?

Answer:

```
multi-user.target
```

---

# What Does multi-user.target Mean?

When the system boots into **multi-user.target**:

- Multiple users can log in.
- Networking starts.
- Most server services start.
- Users log in using a text console.
- No graphical desktop is started.

This is the most common target for Linux servers.

---

# Task 2 – Locate Target Unit Files

Navigate to the systemd unit directory.

```bash
cd /usr/lib/systemd/system
```

List all files.

```bash
ls -l
```

Observe the many different unit files available.

---

# Task 3 – Locate Common Targets

Display the rescue target.

```bash
ls -l rescue.target
```

Display the multi-user target.

```bash
ls -l multi-user.target
```

Display the graphical target.

```bash
ls -l graphical.target
```

Display the reboot target.

```bash
ls -l reboot.target
```

Display the poweroff target.

```bash
ls -l poweroff.target
```

---

# Understanding Important Targets

## rescue.target

Purpose

- Single-user mode
- Root login only
- Networking is not started
- No graphical interface
- Used for troubleshooting and repairing Linux systems

Typical Use Cases

- Reset forgotten passwords
- Repair damaged filesystems
- Fix boot problems
- Perform emergency maintenance

---

## multi-user.target

Purpose

- Multi-user mode
- Text-based login
- Networking enabled
- Server services started
- No graphical desktop

Typical Use Cases

- Production Linux servers
- Web servers
- Database servers
- Application servers

---

## graphical.target

Purpose

- Everything in multi-user.target
- Starts the graphical desktop environment
- Starts the display manager

Typical Use Cases

- Desktop systems
- Linux workstations

---

## reboot.target

Purpose

Reboots the system.

---

## poweroff.target

Purpose

Safely shuts down the system.

---

# Task 4 – List Target Dependencies

Display the dependencies of the graphical target.

```bash
systemctl list-dependencies graphical.target
```

Example

```text
graphical.target

├─display-manager.service

├─multi-user.target

├─NetworkManager.service

├─sshd.service

├─firewalld.service

└─basic.target
```

---

# Understanding Dependencies

A target does not work alone.

It depends on many services and other targets.

For example:

```
graphical.target
```

depends on

```
multi-user.target
```

which depends on

- sshd.service
- firewalld.service
- NetworkManager.service
- chronyd.service
- crond.service

and many more.

This creates a hierarchical dependency tree.

---

# Task 5 – List Dependencies for Another Target

Display the dependencies of the multi-user target.

```bash
systemctl list-dependencies multi-user.target
```

Compare the output with the graphical target.

Question

Which service exists in graphical.target but not in multi-user.target?

Hint:

```
display-manager.service
```

---

# Understanding the Dependency Tree

The output appears as a tree.

Example

```text
graphical.target

└── multi-user.target

    ├── sshd.service

    ├── firewalld.service

    ├── chronyd.service

    └── basic.target
```

Each branch represents another dependency required before the target can fully start.

---

# Understanding the Status Symbols

When running

```bash
systemctl list-dependencies
```

you may see different symbols.

### Green Dot (●)

Meaning

```
Active (running)
```

The service or target is currently running.

---

### White Circle (○)

Meaning

```
Inactive (dead)
```

The service or target is currently not running.

---

### Red Dot

Meaning

```
Failed
```

The service failed to start because of an error.

---

# Task 6 – View the Current Default Target

Run

```bash
systemctl get-default
```

Example Output

```text
multi-user.target
```

Question

Does this match the symbolic link found earlier?

---

# Task 7 – Display All Target Unit Files

List only target files.

```bash
ls *.target
```

Observe how many targets are available on your Linux system.

---

# RHCSA Challenge 1

Find your default boot target.

Record your answer.

---

# RHCSA Challenge 2

Determine whether your system boots into:

- graphical.target
- multi-user.target

---

# RHCSA Challenge 3

Locate the following target files.

- rescue.target
- multi-user.target
- graphical.target
- reboot.target
- poweroff.target

---

# RHCSA Challenge 4

Display the dependencies for

```
multi-user.target
```

Identify at least five services.

---

# RHCSA Challenge 5

Display the dependencies for

```
graphical.target
```

Find one dependency that is not part of the multi-user target.

---

# RHCSA Challenge 6

Run

```bash
systemctl get-default
```

Then verify the result using

```bash
ls -l /etc/systemd/system/default.target
```

Do both commands report the same target?

---

# Knowledge Check

1. What replaced runlevels in modern Linux?

2. What is a target?

3. Which target is commonly used on Linux servers?

4. Which target starts the graphical desktop?

5. Which target is used for system recovery?

6. Which target powers off the system?

7. Which target reboots the system?

8. Where are most target unit files stored?

9. What is the purpose of `default.target`?

10. Which command displays the current default target?

11. Which command lists the dependencies of a target?

12. What does a green dot (●) indicate?

13. What does a white circle (○) indicate?

14. What does a red dot indicate?

---

# Summary

After completing this lab you should be able to:

✅ Explain the difference between runlevels and systemd targets

✅ Identify the default boot target

✅ Locate target configuration files

✅ Understand the purpose of rescue, multi-user, graphical, reboot, and poweroff targets

✅ List target dependencies

✅ Interpret dependency trees

✅ Understand the status symbols displayed by `systemctl`

These concepts are fundamental Linux administration skills and are frequently tested in RHCSA exams and used in real-world Linux server administration.