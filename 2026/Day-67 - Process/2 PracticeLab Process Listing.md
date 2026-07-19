# MODULE 08 – Practice Lab: Listing Linux Processes
> **Hands-on Practice Lab – Viewing Processes with the `ps` Command**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Use the `ps` command to display Linux processes.
- Understand the difference between `ps` and `top`.
- Display processes attached to the current terminal.
- Display all processes running on the system.
- View detailed process information.
- Use `less` to read long command output page by page.
- Use long-listing options with the `ps` command.

---

# 📖 Introduction

When troubleshooting a Linux system, it is important to understand:

- How the Kernel manages processes.
- How processes communicate with the Kernel.
- How processes communicate with each other.
- Which processes are currently running.
- Which user owns a process.
- How much CPU and memory a process is using.
- What state a process is in.

Linux provides several commands for viewing process information.

Two of the most commonly used commands are:

```bash
ps
```

and:

```bash
top
```

---

# 1. What is the `ps` Command?

The `ps` command stands for:

> **Process Status**

It displays information about currently running processes.

Unlike `top`, the `ps` command provides a **single snapshot** of the processes at the moment the command is executed.

---

# Basic Syntax

```bash
ps
```

By default, the command displays processes associated with the current terminal session.

---

# 🔬 Lab 1 – Display Processes on the Current Terminal

Run:

```bash
ps
```

Example Output:

```text
PID TTY          TIME CMD
2180 pts/0    00:00:00 bash
2251 pts/0    00:00:00 ps
```

---

# Understanding the Default `ps` Output

| Column | Meaning |
|--------|---------|
| `PID` | Process ID |
| `TTY` | Terminal associated with the process |
| `TIME` | Total CPU time used |
| `CMD` | Command or process name |

---

# What Does the Default `ps` Command Show?

The default `ps` command normally shows:

- The current shell.
- Commands running in the current terminal.
- The `ps` command itself.

It does not show every process running on the system.

---

# 2. Display All Processes with `ps aux`

To display a much more complete list of processes, run:

```bash
ps aux
```

This syntax uses BSD-style options and does not require a hyphen.

---

# Meaning of `aux`

| Option | Meaning |
|--------|---------|
| `a` | Show processes for all users that are attached to terminals |
| `u` | Display user-oriented details |
| `x` | Include processes without a controlling terminal |

Together:

```text
aux
```

shows almost every process running on the system.

---

# 🔬 Lab 2 – Display All System Processes

Run:

```bash
ps aux
```

A typical header looks like:

```text
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND
```

---

# Process Columns in `ps aux`

| Column | Meaning |
|--------|---------|
| `USER` | Process owner |
| `PID` | Process ID |
| `%CPU` | CPU usage percentage |
| `%MEM` | Physical memory usage percentage |
| `VSZ` | Virtual memory size |
| `RSS` | Resident physical memory |
| `TTY` | Controlling terminal |
| `STAT` | Process state |
| `START` | Process start time |
| `TIME` | Accumulated CPU time |
| `COMMAND` | Full command line |

---

# 3. Use `less` for Long Output

The output of:

```bash
ps aux
```

may be very long.

To read it one page at a time, use a pipe with `less`.

```bash
ps aux | less
```

---

# Understanding the Pipe

The pipe symbol is:

```text
|
```

It sends the output of one command to another command.

Example:

```bash
ps aux | less
```

Flow:

```text
ps aux Output
       │
       ▼
     less
       │
       ▼
Page-by-Page Display
```

---

# Useful `less` Keys

| Key | Action |
|-----|--------|
| `Space` | Move forward one page |
| `b` | Move backward one page |
| `/text` | Search for text |
| `n` | Move to the next search result |
| `q` | Quit |

---

# 🔬 Lab 3 – View Process Output Page by Page

Run:

```bash
ps aux | less
```

Try the following:

- Press `Space` to move forward.
- Press `b` to move backward.
- Type `/sshd` to search for SSH processes.
- Press `q` to exit.

---

# 4. `ps` vs `top`

Both commands display process information, but they work differently.

| Feature | `ps` | `top` |
|---------|------|-------|
| Output Type | Single snapshot | Dynamic real-time display |
| Refreshes Automatically | No | Yes |
| Process Details | Yes | Yes |
| System Summary | Limited | Detailed |
| Best Use | Scripts, filtering, reports | Live troubleshooting |
| Easy to Pipe | Yes | Less commonly used in pipelines |

---

# Example Comparison

## `ps`

```bash
ps aux
```

The output is displayed once and then the command exits.

---

## `top`

```bash
top
```

The output refreshes continuously until you quit.

---

# 5. Long Listing with `ps -l`

To display a long-format listing for processes associated with the current terminal, use:

```bash
ps -l
```

A typical header may include:

```text
F S UID PID PPID C PRI NI ADDR SZ WCHAN TTY TIME CMD
```

---

# Important Long-Listing Columns

| Column | Meaning |
|--------|---------|
| `F` | Process flags |
| `S` | Process state |
| `UID` | User ID |
| `PID` | Process ID |
| `PPID` | Parent Process ID |
| `C` | Processor utilization value |
| `PRI` | Kernel scheduling priority |
| `NI` | Nice value |
| `SZ` | Process memory size |
| `WCHAN` | Kernel function where the process is waiting |
| `TTY` | Controlling terminal |
| `TIME` | Accumulated CPU time |
| `CMD` | Command name |

---

# 6. Long Listing of Many Processes with `ps lax`

To view a long-format listing of many system processes, run:

```bash
ps lax
```

This uses BSD-style options.

---

# Meaning of `lax`

| Option | Meaning |
|--------|---------|
| `l` | Long format |
| `a` | Include processes for other users |
| `x` | Include processes without a controlling terminal |

---

# 🔬 Lab 4 – Display a Long Process Listing

Run:

```bash
ps lax
```

For page-by-page output:

```bash
ps lax | less
```

This provides detailed process information, including:

- Process state
- PID
- PPID
- Priority
- Nice value
- Memory information
- Waiting channel
- Command

---

# 7. Understanding Process States in `ps`

The `STAT` or `S` column shows the process state.

| State | Meaning |
|-------|---------|
| `R` | Running or Runnable |
| `S` | Interruptible Sleep |
| `D` | Uninterruptible Sleep |
| `T` | Stopped |
| `Z` | Zombie |
| `I` | Idle Kernel Thread |

---

# Example

```text
USER       PID %CPU %MEM STAT COMMAND
root         1  0.0  0.1 Ss   /usr/lib/systemd/systemd
root       820  0.0  0.0 S    /usr/sbin/sshd
dev1      1300  0.1  0.1 R+   ps aux
```

The first letter shows the main process state.

Additional characters provide more information.

---

# 8. Common Additional `STAT` Characters

| Character | Meaning |
|-----------|---------|
| `s` | Session leader |
| `+` | Foreground process group |
| `<` | High-priority process |
| `N` | Low-priority process |
| `l` | Multithreaded process |

Example:

```text
Ss
```

means:

- `S` = Sleeping
- `s` = Session leader

---

# 9. Useful `ps` Commands

| Command | Purpose |
|---------|---------|
| `ps` | Show processes in the current terminal |
| `ps aux` | Show nearly all processes with user-oriented details |
| `ps aux \| less` | View all processes page by page |
| `ps -l` | Long listing for the current terminal |
| `ps lax` | Long listing of many system processes |
| `ps lax \| less` | Long listing page by page |
| `ps -ef` | Show all processes in full-format style |
| `ps -eo pid,ppid,stat,cmd` | Show selected process columns |

---

# 10. `ps aux` vs `ps -ef`

Both commands display nearly all processes.

```bash
ps aux
```

uses BSD-style output.

```bash
ps -ef
```

uses UNIX/System V-style output.

---

# `ps -ef` Columns

A typical header:

```text
UID PID PPID C STIME TTY TIME CMD
```

| Column | Meaning |
|--------|---------|
| `UID` | Process owner |
| `PID` | Process ID |
| `PPID` | Parent Process ID |
| `C` | CPU utilization indicator |
| `STIME` | Process start time |
| `TTY` | Controlling terminal |
| `TIME` | CPU time used |
| `CMD` | Full command line |

---

# 11. Display Selected Process Fields

The `-o` option allows you to select specific output columns.

Example:

```bash
ps -eo pid,ppid,user,stat,%cpu,%mem,cmd
```

This displays:

- PID
- PPID
- User
- Process state
- CPU usage
- Memory usage
- Command

---

# 🔬 Lab 5 – Display Selected Columns

Run:

```bash
ps -eo pid,ppid,user,stat,%cpu,%mem,cmd | less
```

This is useful when you only need specific process information.

---

# 12. Search for a Specific Process

Use `grep` to search process output.

Example:

```bash
ps aux | grep sshd
```

This displays lines containing:

```text
sshd
```

---

# Avoid Matching the `grep` Command Itself

A better command is:

```bash
ps aux | grep '[s]shd'
```

or use:

```bash
pgrep -a sshd
```

---

# 🔬 Lab 6 – Search for SSH Processes

Run:

```bash
ps aux | grep '[s]shd'
```

or:

```bash
pgrep -a sshd
```

---

# 13. Sort Processes by CPU Usage

Run:

```bash
ps aux --sort=-%cpu | head
```

This displays the processes using the most CPU.

---

# 14. Sort Processes by Memory Usage

Run:

```bash
ps aux --sort=-%mem | head
```

This displays the processes using the most memory.

---

# 🔬 Lab 7 – Identify Resource-Intensive Processes

Top CPU consumers:

```bash
ps aux --sort=-%cpu | head
```

Top memory consumers:

```bash
ps aux --sort=-%mem | head
```

---

# 15. View Parent and Child Relationships

Run:

```bash
ps -ef --forest
```

or:

```bash
ps auxf
```

This displays processes in a tree-like structure.

---

# Example Process Tree

```text
systemd
 ├─sshd
 │   └─sshd
 │       └─bash
 │           └─ps
 └─crond
```

This helps identify which process created another process.

---

# 🧪 Practice Exercises

## Exercise 1

Display processes associated with the current terminal.

```bash
ps
```

---

## Exercise 2

Display all system processes.

```bash
ps aux
```

---

## Exercise 3

View the output page by page.

```bash
ps aux | less
```

---

## Exercise 4

Display a long listing.

```bash
ps lax | less
```

---

## Exercise 5

Display all processes using full-format output.

```bash
ps -ef | less
```

---

## Exercise 6

Display selected columns.

```bash
ps -eo pid,ppid,user,stat,%cpu,%mem,cmd | less
```

---

## Exercise 7

Search for the SSH service process.

```bash
pgrep -a sshd
```

---

## Exercise 8

Display processes using the most CPU.

```bash
ps aux --sort=-%cpu | head
```

---

## Exercise 9

Display processes using the most memory.

```bash
ps aux --sort=-%mem | head
```

---

## Exercise 10

Display the process hierarchy.

```bash
ps -ef --forest | less
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – Find a Running Service

Search for the SSH daemon:

```bash
ps aux | grep '[s]shd'
```

or:

```bash
pgrep -a sshd
```

---

### Scenario 2 – Identify High CPU Usage

Run:

```bash
ps aux --sort=-%cpu | head
```

---

### Scenario 3 – Identify High Memory Usage

Run:

```bash
ps aux --sort=-%mem | head
```

---

### Scenario 4 – Find Zombie Processes

Run:

```bash
ps -eo pid,ppid,stat,cmd | awk '$3 ~ /^Z/'
```

---

### Scenario 5 – Understand Parent and Child Processes

Run:

```bash
ps -ef --forest | less
```

---

# 📌 Quick Revision

| Command | Purpose |
|---------|---------|
| `ps` | Current terminal processes |
| `ps aux` | Nearly all running processes |
| `ps aux \| less` | Process output page by page |
| `ps -l` | Long listing for current terminal |
| `ps lax` | Long system process listing |
| `ps -ef` | Full-format process listing |
| `ps -eo ...` | Custom process columns |
| `pgrep -a name` | Find processes by name |
| `ps aux --sort=-%cpu \| head` | Top CPU processes |
| `ps aux --sort=-%mem \| head` | Top memory processes |
| `ps -ef --forest` | Parent-child process tree |

---

# 📖 Key Takeaways

- `ps` displays a snapshot of running processes.
- The default `ps` output only shows processes associated with the current terminal.
- `ps aux` displays nearly all processes on the system.
- `ps lax` provides a detailed long listing.
- `top` provides dynamic real-time information, while `ps` provides a one-time snapshot.
- Pipes and `less` make long process output easier to read.
- `grep`, `pgrep`, sorting, and custom output fields make `ps` useful for troubleshooting.

---

# 💡 Remember

> **Think of `ps` as taking a photograph of Linux processes.**
>
> - `ps` takes one snapshot.
> - `top` provides a live video.
> - `ps aux` gives a wide photograph of almost every process.
> - `ps lax` adds more technical details.
> - `less` allows you to examine the photograph one page at a time.
>
> **Golden Rule:**
>
> ```text
> Current Terminal Processes = ps
>
> Nearly All Processes       = ps aux
>
> Detailed Long Listing      = ps lax
>
> Live Process Monitoring    = top
> ```3