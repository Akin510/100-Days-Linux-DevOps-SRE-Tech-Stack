# MODULE 08 – Practice Lab: Linux Process Signals
> **Hands-on Practice Lab – `SIGTSTP`, `SIGSTOP`, `SIGCONT`, `SIGTERM`, and `SIGKILL`**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Understand Linux process signals.
- Suspend a process using `SIGTSTP`.
- Forcefully stop a process using `SIGSTOP`.
- Resume a stopped process using `SIGCONT`.
- Gracefully terminate a process using `SIGTERM`.
- Forcefully terminate a process using `SIGKILL`.
- Send signals using signal names and signal numbers.
- Use both Job IDs and Process IDs with the `kill` command.
- Understand the difference between stopping and terminating a process.

---

# 📖 Introduction

Linux uses **signals** to communicate with running processes.

A signal is a notification sent to a process to request that it perform a particular action.

Signals may be used to:

- Suspend a process.
- Resume a stopped process.
- Terminate a process.
- Forcefully kill a process.
- Reload a service.
- Notify a process about an event.

The `kill` command is commonly used to send signals.

Despite its name, `kill` does not always terminate a process. It can send many different signals.

---

# 1. Basic `kill` Command Syntax

Using a signal name:

```bash
kill -SIGNAL PID
```

Example:

```bash
kill -SIGTERM 2502
```

Using a signal number:

```bash
kill -NUMBER PID
```

Example:

```bash
kill -15 2502
```

Using a shell Job ID:

```bash
kill -SIGNAL %JOB_NUMBER
```

Example:

```bash
kill -SIGCONT %1
```

---

# Job ID vs Process ID

| Identifier | Example | Managed By |
|------------|---------|------------|
| Job ID | `%1` | Current shell |
| Process ID | `2502` | Linux Kernel |

A Job ID must include the percent sign:

```text
%1
```

A PID is written as a normal number:

```text
2502
```

---

# 2. View Available Signals

To display all available Linux signals:

```bash
kill -l
```

Example output includes:

```text
1) SIGHUP
2) SIGINT
9) SIGKILL
15) SIGTERM
18) SIGCONT
19) SIGSTOP
20) SIGTSTP
```

> Signal numbers can vary on some systems and architectures. On standard x86_64 Linux systems, `SIGCONT` is normally 18, `SIGSTOP` is 19, and `SIGTSTP` is 20.

---

# Main Signals Covered in This Lab

| Signal | Common Number | Purpose |
|--------|---------------|---------|
| `SIGTSTP` | `20` | Suspend a foreground process from the terminal |
| `SIGSTOP` | `19` | Forcefully stop a process |
| `SIGCONT` | `18` | Resume a stopped process |
| `SIGTERM` | `15` | Request graceful process termination |
| `SIGKILL` | `9` | Forcefully terminate a process |

---

# 3. Create Practice Processes

Start two long-running background processes:

```bash
sleep 1000 &
sleep 2000 &
```

Display the jobs and their PIDs:

```bash
jobs -l
```

Example Output:

```text
[1]- 2500 Running    sleep 1000 &
[2]+ 2502 Running    sleep 2000 &
```

Here:

- Job 1 has PID `2500`.
- Job 2 has PID `2502`.

Your actual PIDs will be different.

---

# 4. `SIGTSTP` – Terminal Stop Signal

`SIGTSTP` means:

> **Signal Terminal Stop**

It suspends a process.

Common signal number:

```text
20
```

This is the signal normally sent when you press:

```text
Ctrl + Z
```

---

# Important Property of `SIGTSTP`

A process may catch, handle, or ignore `SIGTSTP`.

This means the application may decide how to respond.

---

# 🔬 Lab 1 – Suspend a Foreground Process with `Ctrl + Z`

Run:

```bash
sleep 500
```

Press:

```text
Ctrl + Z
```

Expected Output:

```text
[1]+  Stopped    sleep 500
```

Verify:

```bash
jobs
```

---

# Send `SIGTSTP` Manually

You can also send it using the Job ID:

```bash
kill -SIGTSTP %1
```

Or using the signal number:

```bash
kill -20 %1
```

Using a PID:

```bash
kill -SIGTSTP 2500
```

---

# 5. `SIGSTOP` – Forcefully Stop a Process

`SIGSTOP` suspends a process immediately.

Common signal number:

```text
19
```

Command:

```bash
kill -SIGSTOP PID
```

or:

```bash
kill -19 PID
```

---

# Important Property of `SIGSTOP`

`SIGSTOP` cannot be:

- Caught
- Handled
- Blocked
- Ignored

The Kernel forces the process into a stopped state.

---

# 🔬 Lab 2 – Stop a Process Using `SIGSTOP`

First, start a background process:

```bash
sleep 1000 &
```

Display its PID:

```bash
jobs -l
```

Suppose its PID is:

```text
2500
```

Stop it:

```bash
kill -SIGSTOP 2500
```

Or:

```bash
kill -19 2500
```

Verify:

```bash
jobs
```

Expected state:

```text
Stopped
```

You can also verify with:

```bash
ps -o pid,ppid,stat,cmd -p 2500
```

The `STAT` column should show:

```text
T
```

---

# `SIGTSTP` vs `SIGSTOP`

| Feature | `SIGTSTP` | `SIGSTOP` |
|---------|-----------|-----------|
| Purpose | Suspend a process | Forcefully suspend a process |
| Common Number | `20` | `19` |
| Sent by `Ctrl + Z` | Yes | No |
| Can be handled | Yes | No |
| Can be ignored | Yes | No |
| Kernel-enforced | No | Yes |

---

# 6. `SIGCONT` – Continue a Stopped Process

`SIGCONT` means:

> **Signal Continue**

It resumes a stopped or suspended process.

Common signal number:

```text
18
```

Use `SIGCONT` when a process is in the Stopped state and you want it to continue running.

---

# Basic Syntax

Using a Job ID:

```bash
kill -SIGCONT %1
```

Using a PID:

```bash
kill -SIGCONT 2500
```

Using the signal number:

```bash
kill -18 2500
```

---

# 🔬 Lab 3 – Resume a Stopped Job Using `SIGCONT`

Check current jobs:

```bash
jobs -l
```

Example:

```text
[1]+ 2500 Stopped    sleep 1000
```

Resume it:

```bash
kill -SIGCONT %1
```

Or:

```bash
kill -18 %1
```

Verify:

```bash
jobs
```

Expected Output:

```text
[1]+  Running    sleep 1000 &
```

---

# What Happens After `SIGCONT`?

- The stopped process returns to a runnable state.
- The scheduler may assign CPU time to it.
- If it is a background job, it continues in the background.
- The shell prompt remains available.

---

# `SIGCONT` Flow

```text
Stopped Process
      │
      ▼
kill -SIGCONT PID
      │
      ▼
Process Becomes Runnable
      │
      ▼
Process Continues Execution
```

---

# 7. `SIGTERM` – Graceful Termination

`SIGTERM` means:

> **Signal Terminate**

Common signal number:

```text
15
```

This is the default signal sent by the `kill` command.

These two commands are equivalent:

```bash
kill PID
```

```bash
kill -SIGTERM PID
```

```bash
kill -15 PID
```

---

# Why Is `SIGTERM` Preferred?

`SIGTERM` gives the process an opportunity to:

- Save data.
- Close open files.
- Release resources.
- Remove temporary files.
- Shut down cleanly.

A process may catch or handle `SIGTERM`.

---

# 🔬 Lab 4 – Terminate a Running Job Using `SIGTERM`

Display jobs:

```bash
jobs -l
```

Suppose Job 2 has PID:

```text
2502
```

Terminate it using the PID:

```bash
kill -SIGTERM 2502
```

Or:

```bash
kill -15 2502
```

You can also use the Job ID:

```bash
kill -SIGTERM %2
```

Verify:

```bash
jobs
```

You may see:

```text
[2]+  Terminated    sleep 2000
```

---

# `SIGTERM` Flow

```text
Running Process
      │
      ▼
SIGTERM Sent
      │
      ▼
Process Receives Termination Request
      │
      ├── Performs Cleanup
      │
      └── Exits Gracefully
```

---

# 8. `SIGKILL` – Forceful Termination

`SIGKILL` means:

> **Signal Kill**

Common signal number:

```text
9
```

It immediately terminates a process.

Command:

```bash
kill -SIGKILL PID
```

Or:

```bash
kill -9 PID
```

---

# Important Property of `SIGKILL`

`SIGKILL` cannot be:

- Caught
- Handled
- Blocked
- Ignored

The Kernel terminates the process immediately.

The process does not get an opportunity to clean up.

---

# 🔬 Lab 5 – Forcefully Kill a Process

Suppose a remaining process has PID:

```text
2500
```

Kill it:

```bash
kill -SIGKILL 2500
```

Or:

```bash
kill -9 2500
```

Verify:

```bash
ps -p 2500
```

No process should be displayed.

Check shell jobs:

```bash
jobs
```

You may see:

```text
[1]+  Killed    sleep 1000
```

---

# When Should `SIGKILL` Be Used?

Use `SIGKILL` only when:

- `SIGTERM` does not work.
- The process is unresponsive.
- The process is stuck.
- Immediate termination is necessary.

Always try `SIGTERM` first.

---

# Recommended Termination Procedure

```text
Step 1: Send SIGTERM
        │
        ▼
Wait and Verify
        │
        ├── Process Stops ──► Finished
        │
        └── Process Still Running
                     │
                     ▼
Step 2: Send SIGKILL
```

Commands:

```bash
kill -15 PID
```

Check:

```bash
ps -p PID
```

If the process still exists:

```bash
kill -9 PID
```

---

# `SIGTERM` vs `SIGKILL`

| Feature | `SIGTERM` | `SIGKILL` |
|---------|-----------|-----------|
| Signal Number | `15` | `9` |
| Termination Type | Graceful request | Immediate force |
| Can be handled | Yes | No |
| Can be ignored | Yes | No |
| Allows cleanup | Yes | No |
| Recommended first | Yes | No |
| Use case | Normal termination | Unresponsive process |

---

# 9. Stop, Continue, and Terminate Flow

```text
Running Process
      │
      ├── SIGTSTP ──► Stopped
      │
      ├── SIGSTOP ──► Stopped
      │                  │
      │                  └── SIGCONT ──► Running
      │
      ├── SIGTERM ──► Graceful Termination
      │
      └── SIGKILL ──► Immediate Termination
```

---

# 10. Job IDs and PIDs in Practice

Suppose:

```bash
jobs -l
```

shows:

```text
[1]- 2500 Stopped    sleep 1000
[2]+ 2502 Running    sleep 2000 &
```

You may use either form.

## With Job IDs

```bash
kill -SIGCONT %1
```

```bash
kill -SIGTERM %2
```

---

## With PIDs

```bash
kill -SIGCONT 2500
```

```bash
kill -SIGTERM 2502
```

---

# 11. Verify Process State

Use:

```bash
ps -o pid,ppid,stat,cmd -p PID
```

Example:

```bash
ps -o pid,ppid,stat,cmd -p 2500
```

Typical states:

| `STAT` | Meaning |
|--------|---------|
| `R` | Running or Runnable |
| `S` | Sleeping |
| `T` | Stopped |
| `Z` | Zombie |

---

# 12. View Signal Information

List all signals:

```bash
kill -l
```

Convert a number to a signal name:

```bash
kill -l 15
```

Expected Output:

```text
TERM
```

Convert another:

```bash
kill -l 9
```

Expected Output:

```text
KILL
```

---

# 🧪 Practice Exercises

## Exercise 1 – Start Two Processes

```bash
sleep 500 &
sleep 600 &
```

Display jobs and PIDs:

```bash
jobs -l
```

---

## Exercise 2 – Suspend Job 1 with `SIGTSTP`

```bash
kill -SIGTSTP %1
```

Verify:

```bash
jobs
```

---

## Exercise 3 – Resume Job 1 with `SIGCONT`

```bash
kill -SIGCONT %1
```

Verify:

```bash
jobs
```

---

## Exercise 4 – Stop Job 2 with `SIGSTOP`

```bash
kill -SIGSTOP %2
```

Verify:

```bash
jobs
```

---

## Exercise 5 – Continue Job 2

```bash
kill -SIGCONT %2
```

---

## Exercise 6 – Terminate Job 1 Gracefully

```bash
kill -SIGTERM %1
```

---

## Exercise 7 – Kill Job 2 Forcefully

```bash
kill -SIGKILL %2
```

---

## Exercise 8 – Verify All Jobs

```bash
jobs
```

```bash
ps -ef | grep '[s]leep'
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – Process Is Stopped

Check:

```bash
jobs -l
```

Resume it:

```bash
kill -SIGCONT %1
```

or:

```bash
bg %1
```

---

### Scenario 2 – Process Does Not Stop with `Ctrl + Z`

Send `SIGSTOP`:

```bash
kill -SIGSTOP PID
```

---

### Scenario 3 – Process Must Be Terminated Cleanly

Use:

```bash
kill -SIGTERM PID
```

Wait and verify:

```bash
ps -p PID
```

---

### Scenario 4 – Process Ignores `SIGTERM`

Use:

```bash
kill -SIGKILL PID
```

This should be the last resort.

---

### Scenario 5 – `kill %1` Reports No Such Job

The Job ID may belong to another shell or may already have completed.

Check:

```bash
jobs
```

Use the PID instead:

```bash
ps aux | grep process_name
```

Then:

```bash
kill -15 PID
```

---

### Scenario 6 – Signal Number Is Uncertain

Check the current system:

```bash
kill -l
```

Do not assume numbers on unusual architectures.

---

# ⚠️ Important Safety Notes

- Always confirm the PID before sending a signal.
- Do not send `SIGKILL` first unless absolutely necessary.
- Killing critical processes may cause:
  - Data loss
  - Service interruption
  - Corrupted files
  - User disconnection
  - System instability
- Avoid killing PID 1.
- On production systems, check the service before terminating its process.

For systemd services, it is often better to use:

```bash
systemctl stop service_name
```

instead of killing the process manually.

---

# 📌 Quick Revision

| Signal | Number | Purpose | Can Be Handled? |
|--------|-------:|---------|-----------------|
| `SIGTSTP` | `20` | Suspend from terminal | Yes |
| `SIGSTOP` | `19` | Forcefully stop | No |
| `SIGCONT` | `18` | Resume stopped process | Yes |
| `SIGTERM` | `15` | Graceful termination | Yes |
| `SIGKILL` | `9` | Immediate termination | No |

---

# Common Commands

| Command | Purpose |
|---------|---------|
| `kill -l` | List signals |
| `kill -SIGTSTP PID` | Suspend a process |
| `kill -SIGSTOP PID` | Forcefully stop a process |
| `kill -SIGCONT PID` | Resume a stopped process |
| `kill -SIGTERM PID` | Gracefully terminate a process |
| `kill -15 PID` | Same as `SIGTERM` |
| `kill -SIGKILL PID` | Forcefully terminate a process |
| `kill -9 PID` | Same as `SIGKILL` |
| `jobs -l` | Show jobs and PIDs |
| `ps -p PID` | Verify whether a process exists |

---

# 📖 Key Takeaways

- Linux uses signals to control running processes.
- `SIGTSTP` suspends a process and may be handled by the application.
- `SIGSTOP` forcefully suspends a process and cannot be ignored.
- `SIGCONT` resumes a stopped process.
- `SIGTERM` requests graceful termination.
- `SIGKILL` immediately forces termination.
- Use `SIGTERM` before `SIGKILL`.
- Signals can be sent using either their names or numbers.
- Job IDs require `%`, while PIDs do not.
- Always verify the target process before sending a signal.

---

# 💡 Remember

> **Think of process signals as instructions given to an employee.**
>
> - `SIGTSTP` – “Please pause your work.”
> - `SIGSTOP` – “Stop immediately.”
> - `SIGCONT` – “Continue your work.”
> - `SIGTERM` – “Finish safely and leave.”
> - `SIGKILL` – “Leave immediately without cleanup.”
>
> **Golden Signal Flow:**
>
> ```text
> Running
>    │
>    ├── SIGTSTP/SIGSTOP ──► Stopped
>    │                           │
>    │                           └── SIGCONT ──► Running
>    │
>    ├── SIGTERM ───────────► Graceful Exit
>    │
>    └── SIGKILL ───────────► Immediate Exit
> ```
>
> **Always use the least destructive signal that can complete the task.**