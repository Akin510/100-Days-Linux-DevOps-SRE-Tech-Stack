# MODULE 08 – Practice Lab: Control Jobs
> **Hands-on Practice Lab – Foreground and Background Processes**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Understand Linux job control.
- Identify Foreground Processes.
- Identify Background Processes.
- Run commands in the foreground.
- Run commands in the background.
- Understand how the terminal interacts with running processes.
- Use the ampersand (`&`) to start a background job.
- Continue using the terminal while another process is running.

---

# 📖 Introduction

When you start a command or program in Linux, it runs as a process.

That process can run in one of two main ways:

1. **Foreground Process**
2. **Background Process**

By default, commands run in the foreground.

A foreground process occupies the terminal until it finishes.

A background process runs without occupying the terminal, allowing you to continue executing other commands.

---

# 1. What is Job Control?

**Job Control** is a feature of the Linux shell that allows users to manage processes started from the current terminal.

Using job control, you can:

- Run a command in the foreground.
- Run a command in the background.
- Suspend a running process.
- Resume a suspended process.
- Move jobs between the foreground and background.
- View active shell jobs.

---

# Process vs Job

A **Process** is a running instance of a program.

A **Job** is a command or pipeline started from the current shell.

One job may contain one or more processes.

Example:

```bash
cat file1 | grep error
```

This is one shell job, but it contains multiple processes.

---

# 2. Foreground Process

A **Foreground Process** runs directly in the current terminal.

By default, every command starts in the foreground.

Examples:

```bash
pwd
```

```bash
ls
```

```bash
./backup.sh
```

While a foreground process is running:

- It receives input from the keyboard.
- It sends output to the screen.
- It occupies the terminal.
- The shell prompt is not available.
- You normally cannot start another command in the same terminal until it finishes.

---

# 🔬 Lab 1 – Run a Foreground Command

Run:

```bash
pwd
```

The command executes in the foreground.

Expected Output:

```text
/home/dev1
```

The process completes quickly, and the shell prompt returns.

---

# 🔬 Lab 2 – Run `ls` in the Foreground

Run:

```bash
ls
```

What happens?

1. Bash receives the command.
2. Bash starts an `ls` process.
3. The process runs in the foreground.
4. The output appears on the screen.
5. The process exits.
6. The shell prompt returns.

---

# Foreground Process Flow

```text
User Enters Command
        │
        ▼
Shell Starts Process
        │
        ▼
Process Runs in Foreground
        │
        ▼
Terminal Is Occupied
        │
        ▼
Process Finishes
        │
        ▼
Shell Prompt Returns
```

---

# 3. Foreground Process and Terminal Input

A foreground process is connected to the terminal.

It can:

- Read keyboard input.
- Display output.
- Receive terminal-generated signals.

For example, a program may wait for user input:

```bash
cat
```

The `cat` command waits for you to type text.

Press:

```text
Ctrl + D
```

to send end-of-file and finish the command.

---

# 4. Problem with Long-Running Foreground Jobs

Suppose you run a long backup script:

```bash
./backup.sh
```

If the script takes 30 minutes:

- The terminal remains occupied.
- The prompt does not return.
- You cannot run another command in the same terminal.
- You must wait for the script to finish.

This is acceptable for short commands but inconvenient for long-running jobs.

---

# 5. Background Process

A **Background Process** runs without occupying the terminal.

It normally does not receive direct keyboard input from the terminal.

The shell prompt returns immediately, allowing you to run other commands.

A background process is useful for:

- Backups
- Long scripts
- File transfers
- Compression jobs
- Data processing
- Long-running maintenance tasks

---

# 6. Start a Background Process with `&`

To run a command in the background, add an ampersand (`&`) at the end.

Syntax:

```bash
command &
```

Example:

```bash
ls &
```

The shell starts the command in the background and immediately returns the prompt.

---

# 🔬 Lab 3 – Run `ls` in the Background

Run:

```bash
ls &
```

Example Output:

```text
[1] 2450
```

Then the command output may appear.

---

# Understanding the Output

```text
[1] 2450
```

| Value | Meaning |
|-------|---------|
| `[1]` | Shell Job Number |
| `2450` | Process ID (PID) |

The shell uses the job number to manage jobs started from that terminal.

The operating system uses the PID to identify the process.

---

# Background Process Flow

```text
User Enters Command &
        │
        ▼
Shell Starts Background Job
        │
        ├── Process Continues Running
        │
        └── Shell Prompt Returns Immediately
                     │
                     ▼
            User Runs Other Commands
```

---

# 7. Foreground vs Background Processes

| Feature | Foreground Process | Background Process |
|---------|--------------------|--------------------|
| Occupies terminal | Yes | No |
| Receives keyboard input | Yes | Normally no |
| Output appears on screen | Yes | May appear unless redirected |
| Shell prompt available | No, until completion | Yes |
| Started by default | Yes | No |
| Start syntax | `command` | `command &` |
| Best for | Short or interactive commands | Long-running, non-interactive tasks |

---

# 8. Practical Long-Running Command

The `sleep` command is useful for practicing job control.

Run:

```bash
sleep 60
```

This process runs in the foreground for 60 seconds.

During this time:

- The terminal is occupied.
- The shell prompt is unavailable.

You can interrupt it using:

```text
Ctrl + C
```

---

# 🔬 Lab 4 – Run `sleep` in the Background

Run:

```bash
sleep 60 &
```

Example Output:

```text
[1] 2520
```

The prompt returns immediately.

Now run:

```bash
date
```

The `sleep` process continues in the background while you execute another command.

---

# 9. List Background Jobs with `jobs`

To display jobs started from the current shell:

```bash
jobs
```

Example Output:

```text
[1]+  Running    sleep 60 &
```

---

# Understanding `jobs` Output

| Field | Meaning |
|-------|---------|
| `[1]` | Job number |
| `+` | Current/default job |
| `Running` | Job state |
| `sleep 60 &` | Command |

---

# 🔬 Lab 5 – View Current Jobs

Start two background jobs:

```bash
sleep 100 &
sleep 200 &
```

Now run:

```bash
jobs
```

Example Output:

```text
[1]-  Running    sleep 100 &
[2]+  Running    sleep 200 &
```

---

# 10. Job Number vs PID

Linux uses two identifiers:

| Identifier | Example | Used By |
|------------|---------|---------|
| Job Number | `%1` | Current shell |
| PID | `2520` | Linux Kernel |

The job number only exists inside the shell session where the job was started.

The PID identifies the process across the operating system.

---

# 11. Bring a Background Job to the Foreground

Use the `fg` command.

Syntax:

```bash
fg %job_number
```

Example:

```bash
fg %1
```

This brings Job 1 into the foreground.

The terminal becomes occupied by that job.

---

# 🔬 Lab 6 – Move a Job to the Foreground

Start:

```bash
sleep 300 &
```

Check:

```bash
jobs
```

Bring it to the foreground:

```bash
fg %1
```

The terminal is now occupied by the `sleep` process.

Press:

```text
Ctrl + C
```

to terminate it.

---

# 12. Suspend a Foreground Process

A running foreground process can be suspended using:

```text
Ctrl + Z
```

This normally sends the `SIGTSTP` signal.

Example:

```bash
sleep 300
```

Press:

```text
Ctrl + Z
```

Expected Output:

```text
[1]+  Stopped    sleep 300
```

The process is paused, not terminated.

---

# 13. Resume a Suspended Job in the Background

Use:

```bash
bg %1
```

Example:

```bash
bg %1
```

Expected Output:

```text
[1]+ sleep 300 &
```

The suspended job continues running in the background.

---

# Job-Control Flow

```text
Foreground Job
      │
      │ Ctrl + Z
      ▼
Stopped Job
      │
      ├── bg %1 ──► Background
      │
      └── fg %1 ──► Foreground
```

---

# 14. Resume a Suspended Job in the Foreground

Use:

```bash
fg %1
```

This resumes the job in the foreground.

---

# 🔬 Lab 7 – Suspend and Resume a Job

Run:

```bash
sleep 500
```

Press:

```text
Ctrl + Z
```

Check:

```bash
jobs
```

Resume in background:

```bash
bg %1
```

Bring it back to foreground:

```bash
fg %1
```

Terminate it:

```text
Ctrl + C
```

---

# 15. Important Keyboard Shortcuts

| Shortcut | Purpose |
|----------|---------|
| `Ctrl + C` | Terminate the foreground process |
| `Ctrl + Z` | Suspend the foreground process |
| `Ctrl + D` | Send end-of-file to an interactive command |
| `Ctrl + L` | Clear the terminal screen |

---

# `Ctrl + C` vs `Ctrl + Z`

| Shortcut | Result |
|----------|--------|
| `Ctrl + C` | Stops and terminates the process |
| `Ctrl + Z` | Pauses the process and leaves it as a stopped job |

---

# 16. Background Output

A background process can still write output to the terminal.

Example:

```bash
for i in {1..5}; do echo "Message $i"; sleep 2; done &
```

The prompt returns, but messages continue appearing on the screen.

This can make the terminal difficult to use.

---

# 17. Redirect Background Output

To prevent background output from appearing on the terminal, redirect it to a file.

Example:

```bash
./backup.sh > backup.log 2>&1 &
```

### Explanation

| Part | Meaning |
|------|---------|
| `> backup.log` | Send standard output to `backup.log` |
| `2>&1` | Send standard error to the same location |
| `&` | Run in the background |

---

# Another Example

```bash
find / -name "*.conf" > find-results.txt 2> find-errors.txt &
```

This sends:

- Normal output to `find-results.txt`
- Errors to `find-errors.txt`
- Process to the background

---

# 18. Check Whether a Background Process Is Running

Use:

```bash
jobs
```

or:

```bash
ps
```

or:

```bash
ps aux | grep '[s]leep'
```

or:

```bash
pgrep -a sleep
```

---

# 19. Terminate a Background Job

Use the `kill` command with a job number:

```bash
kill %1
```

Or use the PID:

```bash
kill 2520
```

Verify:

```bash
jobs
```

---

# 🔬 Lab 8 – Stop a Background Job

Start:

```bash
sleep 600 &
```

Check:

```bash
jobs
```

Terminate the job:

```bash
kill %1
```

Verify:

```bash
jobs
```

---

# 20. What Happens When a Background Job Finishes?

When a background job finishes, Bash may display a message:

```text
[1]+  Done    sleep 60
```

This indicates:

- The process completed.
- The shell updated the job status.
- The job no longer appears as running.

---

# 21. Background Job and Logout

A normal background job may terminate when the terminal or shell session closes.

For jobs that must continue after logout, tools such as these are commonly used:

```bash
nohup
```

```bash
screen
```

```bash
tmux
```

For production services, use:

```text
systemd
```

rather than relying on shell background jobs.

---

# 22. Run a Job with `nohup`

Example:

```bash
nohup ./backup.sh > backup.log 2>&1 &
```

This helps the process continue after the terminal disconnects.

The process should still be verified after logout because behavior can depend on the shell and environment.

---

# 23. Practical Example: Backup Script

## Foreground Execution

```bash
./backup.sh
```

Result:

- Terminal is occupied.
- Prompt is unavailable.
- You must wait for completion.

---

## Background Execution

```bash
./backup.sh > backup.log 2>&1 &
```

Result:

- Backup runs in the background.
- Terminal remains available.
- Output is stored in `backup.log`.
- Other commands can be executed.

---

# 24. Complete Job-Control Workflow

```text
Start Long Command
      │
      ├── command ─────────► Foreground
      │                        │
      │                        ├── Ctrl + C ─► Terminated
      │                        │
      │                        └── Ctrl + Z ─► Stopped
      │                                           │
      │                                           ├── bg %1 ─► Background
      │                                           └── fg %1 ─► Foreground
      │
      └── command & ───────► Background
                               │
                               ├── fg %1 ─► Foreground
                               └── kill %1 ─► Terminated
```

---

# 🧪 Practice Exercises

## Exercise 1 – Foreground Job

Run:

```bash
sleep 30
```

Observe that the terminal is occupied.

---

## Exercise 2 – Background Job

Run:

```bash
sleep 30 &
```

Verify that the prompt returns immediately.

---

## Exercise 3 – List Jobs

Run:

```bash
jobs
```

---

## Exercise 4 – Start Multiple Jobs

```bash
sleep 100 &
sleep 200 &
sleep 300 &
```

List them:

```bash
jobs
```

---

## Exercise 5 – Bring a Job to the Foreground

```bash
fg %1
```

---

## Exercise 6 – Suspend the Foreground Job

Press:

```text
Ctrl + Z
```

---

## Exercise 7 – Resume It in the Background

```bash
bg %1
```

---

## Exercise 8 – Terminate a Background Job

```bash
kill %1
```

---

## Exercise 9 – Redirect Background Output

```bash
for i in {1..10}; do echo "Line $i"; sleep 1; done > output.log 2>&1 &
```

View the output:

```bash
tail -f output.log
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – Terminal Is Occupied

A long-running process is in the foreground.

Press:

```text
Ctrl + Z
```

Then run:

```bash
bg
```

---

### Scenario 2 – Need the Background Job in the Foreground

Check jobs:

```bash
jobs
```

Then:

```bash
fg %1
```

---

### Scenario 3 – Background Output Is Disturbing the Terminal

Restart the command with redirection:

```bash
command > command.log 2>&1 &
```

---

### Scenario 4 – Need to Stop a Background Job

Run:

```bash
kill %1
```

or:

```bash
kill PID
```

---

### Scenario 5 – Job Disappeared After Logout

For long-running jobs, consider:

```bash
nohup command > output.log 2>&1 &
```

or use:

```text
screen
tmux
systemd
```

---

# 📌 Quick Revision

| Command or Key | Purpose |
|----------------|---------|
| `command` | Run in the foreground |
| `command &` | Run in the background |
| `jobs` | List current shell jobs |
| `fg %1` | Bring Job 1 to the foreground |
| `bg %1` | Resume Job 1 in the background |
| `kill %1` | Terminate Job 1 |
| `Ctrl + C` | Terminate foreground process |
| `Ctrl + Z` | Suspend foreground process |
| `nohup command &` | Help a command continue after logout |
| `> file 2>&1 &` | Redirect output and run in background |

---

# 📖 Key Takeaways

- Commands run in the foreground by default.
- A foreground process occupies the terminal.
- A background process allows the terminal to remain available.
- Add `&` to start a command in the background.
- Use `jobs` to list jobs started from the current shell.
- Use `fg` to bring a job to the foreground.
- Use `bg` to resume a suspended job in the background.
- `Ctrl + Z` suspends a process.
- `Ctrl + C` terminates a foreground process.
- Background output should usually be redirected to a file.
- Shell jobs are useful for temporary tasks, while long-running production services should normally be managed with `systemd`.

---

# 💡 Remember

> **Think of the terminal as a service counter.**
>
> - A **Foreground Process** stands at the counter and occupies it until its work is complete.
> - A **Background Process** leaves its work with the staff and allows you to use the counter for another task.
>
> **Golden Rules:**
>
> ```text
> Foreground Job = command
>
> Background Job = command &
>
> List Jobs      = jobs
>
> Bring Forward  = fg %job
>
> Send Back      = bg %job
> ```
>
> **Use foreground execution for short or interactive commands, and background execution for long, non-interactive jobs.**