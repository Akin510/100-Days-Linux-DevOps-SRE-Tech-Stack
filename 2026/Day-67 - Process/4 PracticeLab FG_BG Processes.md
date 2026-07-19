# MODULE 08 – Practice Lab: Additional Foreground and Background Processes
> **Hands-on Practice Lab – Managing Multiple Jobs Using `Ctrl + C`, `Ctrl + Z`, `fg`, `bg`, and `jobs`**

---

# 🎯 Lab Objectives

In this practice lab, you will learn how to:

- Create a continuous process using a `while` loop.
- Run a process in the foreground.
- Understand how a foreground process occupies the terminal.
- Terminate a process using `Ctrl + C`.
- Suspend a process using `Ctrl + Z`.
- View shell jobs using the `jobs` command.
- Resume a stopped job in the foreground using `fg`.
- Resume a stopped job in the background using `bg`.
- Manage multiple stopped and running jobs.
- Move a background job to the foreground.
- Understand what happens when multiple processes write to the same file.

---

# 📖 Introduction

In Linux, a process can run in two primary modes:

1. **Foreground**
2. **Background**

A foreground process occupies the terminal and normally receives keyboard input.

A background process runs without occupying the terminal, allowing the user to execute additional commands simultaneously.

In this lab, we will create a simple continuous loop and use it to practice Linux Job Control.

---

# 1. Create a Continuous `while` Loop

The following command continuously writes text to a file every second.

```bash
while true
do
    echo "Learning Processes" >> /tmp/process.log
    sleep 1
done
```

The same command can also be written on a single line:

```bash
while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

---

# Understanding the Loop

| Component | Description |
|-----------|-------------|
| `while true` | Run the loop forever. |
| `do` | Begin the commands inside the loop. |
| `echo "Learning Processes"` | Display the specified message. |
| `>> /tmp/process.log` | Append the message to the end of the file. |
| `sleep 1` | Pause for one second before the next iteration. |
| `done` | End the loop block and repeat. |

---

# Why Use `>>`?

The command uses:

```text
>>
```

This operator **appends** new content to the end of the file.

If you use:

```text
>
```

the file is overwritten each time the loop executes, meaning only the most recent line remains.

---

# 🔬 Lab 1 – Run the Loop in the Foreground

Execute:

```bash
while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

The command will continue running indefinitely.

Observe the following:

- The terminal is occupied.
- The shell prompt is unavailable.
- You cannot execute another command in the same terminal.
- The process executing the loop is running in the foreground.

---

# Foreground Process Flow

```text
User Starts the Loop
        │
        ▼
Shell Creates a Process
        │
        ▼
Process Runs in the Foreground
        │
        ▼
Terminal Becomes Occupied
        │
        ▼
Loop Writes to the File Every Second
```

---

# 2. Monitor the File from Another Terminal

Open a second terminal window.

Run:

```bash
tail -f /tmp/process.log
```

Expected Output:

```text
Learning Processes
Learning Processes
Learning Processes
Learning Processes
```

A new line appears every second.

---

# Understanding `tail -f`

| Component | Description |
|-----------|-------------|
| `tail` | Displays the end of a file. |
| `-f` | Continuously follows the file as new data is added. |

This proves that the foreground process is continuously updating the file.

---

# 3. Terminate the Foreground Process with `Ctrl + C`

Return to the terminal where the loop is running.

Press:

```text
Ctrl + C
```

This normally sends the following signal:

```text
SIGINT
```

The process is immediately terminated.

---

# What Happens After Pressing `Ctrl + C`?

- The loop stops.
- The process terminates.
- The shell prompt returns.
- `/tmp/process.log` is no longer updated.

Verify from the second terminal:

```bash
tail -f /tmp/process.log
```

The output should stop updating.

---

# `Ctrl + C` Process Flow

```text
Foreground Process Running
        │
        ▼
User Presses Ctrl + C
        │
        ▼
SIGINT Signal Sent
        │
        ▼
Process Terminates
        │
        ▼
Shell Prompt Returns
```

---

# 4. Start the Loop Again

Run the loop again:

```bash
while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

A new process is created.

The log file begins updating again.

---

# 5. Suspend the Process with `Ctrl + Z`

Instead of terminating the process, press:

```text
Ctrl + Z
```

Expected Output:

```text
[1]+ Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

The process has now been suspended.

---

# Difference Between `Ctrl + C` and `Ctrl + Z`

| Shortcut | Result |
|----------|--------|
| `Ctrl + C` | Terminates the process. |
| `Ctrl + Z` | Suspends (pauses) the process. |

A suspended process still exists but is no longer actively executing.

---

# 6. View the Stopped Job

Run:

```bash
jobs
```

Example Output:

```text
[1]+ Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

---

# Understanding the `jobs` Output

| Field | Description |
|-------|-------------|
| `[1]` | Job Number |
| `+` | Current (default) job |
| `Stopped` | Job state |
| Command | Command associated with the job |

---

# 7. Resume the Job in the Foreground

Run:

```bash
fg %1
```

The process resumes in the foreground.

Observe:

- The terminal is occupied again.
- The shell prompt disappears.
- `/tmp/process.log` resumes updating.

---

# Understanding `fg %1`

| Component | Description |
|-----------|-------------|
| `fg` | Moves or resumes a job in the foreground. |
| `%1` | Refers to Job Number 1. |

---

# 8. Terminal Disconnection and Foreground Processes

A foreground process is directly associated with the current terminal session.

If the terminal is closed or disconnected, the process may receive the following signal:

```text
SIGHUP
```

The process usually terminates unless it has been protected using tools such as:

- `nohup`
- `screen`
- `tmux`
- `systemd`

---

# 9. Suspend the Job Again

While the loop is running in the foreground, press:

```text
Ctrl + Z
```

The job returns to the **Stopped** state.

Verify it by running:

```bash
jobs
```

---

# 10. Start a Second Foreground Process

Run the same loop again:

```bash
while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

Then press:

```text
Ctrl + Z
```

Now display all jobs:

```bash
jobs
```

Example Output:

```text
[1]- Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
[2]+ Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done
```

You now have two stopped jobs.

---

# Understanding the `+` and `-` Symbols

| Symbol | Meaning |
|---------|---------|
| `+` | Current (default) job |
| `-` | Previous job |

If you execute `fg` or `bg` without specifying a job number, Bash normally operates on the **current (`+`)** job.

---

# 11. Resume a Job in the Background

Resume Job 2 in the background:

```bash
bg %2
```

Example Output:

```text
[2]+ while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done &
```

Notice the ampersand (`&`) at the end of the command.

It indicates that the process is now running in the **background**.

---

# What Happens in the Background?

- The loop resumes execution.
- The log file continues to update.
- The shell prompt remains available.
- You can execute additional commands without interruption.

Verify the job status:

```bash
jobs
```

Example Output:

```text
[1]+ Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done

[2]- Running while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done &
```

---

# 12. Resume Another Job in the Foreground

Suppose Job 1 is still stopped.

Bring it back to the foreground:

```bash
fg %1
```

Now:

- Job 1 runs in the foreground.
- Job 2 continues running in the background.
- Both processes write to the same log file.

---

# Two Processes Writing to the Same File

```text
Foreground Process
        │
        ├── Writes "Learning Processes"
        │
Background Process
        │
        └── Writes "Learning Processes"
                    │
                    ▼
            /tmp/process.log
```

Since two independent processes are writing to the same file, the file updates more rapidly.

---

# 13. Suspend the Foreground Job Again

Press:

```text
Ctrl + Z
```

Display all jobs:

```bash
jobs
```

You may now have:

- One running background job.
- One or more stopped jobs.

---

# 14. Start a Third Loop with Different Output

Execute:

```bash
while true; do echo "This is my third script" >> /tmp/process.log; sleep 1; done
```

The log file now contains multiple messages.

Example:

```text
Learning Processes
This is my third script
Learning Processes
This is my third script
```

Suspend the third loop:

```text
Ctrl + Z
```

---

# 15. Display All Jobs

Run:

```bash
jobs
```

Example Output:

```text
[1] Stopped while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done

[2]- Running while true; do echo "Learning Processes" >> /tmp/process.log; sleep 1; done &

[3]+ Stopped while true; do echo "This is my third script" >> /tmp/process.log; sleep 1; done
```

---

# 16. Move a Background Process to the Foreground

Suppose Job 2 is currently running in the background.

Bring it to the foreground:

```bash
fg %2
```

The process now:

- Occupies the terminal.
- Can receive keyboard input.
- Continues execution in the foreground.

The remaining stopped jobs stay suspended.

To suspend it again:

```text
Ctrl + Z
```

---

# Background to Foreground Flow

```text
Background Job
      │
      ▼
fg %2
      │
      ▼
Foreground Job
      │
      ▼
Terminal Occupied
```

---

# 17. Important Job Control Commands

| Command | Purpose |
|----------|---------|
| `jobs` | Display jobs associated with the current shell |
| `fg` | Resume the current job in the foreground |
| `fg %1` | Resume Job 1 in the foreground |
| `bg` | Resume the current stopped job in the background |
| `bg %1` | Resume Job 1 in the background |
| `kill %1` | Send a termination signal to Job 1 |
| `jobs -l` | Display jobs along with their Process IDs (PIDs) |

---

# 18. Job Number vs Process ID (PID)

A Job Number and a Process ID are not the same.

| Identifier | Example | Managed By |
|------------|---------|------------|
| Job Number | `%1` | Bash Shell |
| Process ID (PID) | `4205` | Linux Kernel |

To display both values:

```bash
jobs -l
```

Example Output:

```text
[1]+ 4205 Running sleep 300 &
```

The Job Number is used by the shell, while the PID uniquely identifies the process within the operating system.

---

---

# 19. Terminate All Practice Jobs

First, display all active jobs:

```bash
jobs
```

Terminate each job individually:

```bash
kill %1
kill %2
kill %3
```

Verify that the jobs have been terminated:

```bash
jobs
```

Sometimes Bash displays the completion status only after you press **Enter** once more.

---

# Alternative: Terminate All Jobs in the Current Shell

A convenient method to terminate every job associated with the current shell is:

```bash
kill $(jobs -p)
```

This command:

- Retrieves the Process IDs (PIDs) of all jobs in the current shell.
- Sends the default termination signal (`SIGTERM`) to each process.

> **Note:** Use this command carefully, as it terminates all background and suspended jobs in the current shell session.

---

# 20. Clean Up the Practice File

After completing the lab, remove the log file.

Execute:

```bash
rm -f /tmp/process.log
```

Verify that the file has been removed:

```bash
ls -l /tmp/process.log
```

Expected Output:

```text
ls: cannot access '/tmp/process.log': No such file or directory
```

This confirms that the practice file has been successfully deleted.

---

# 21. Complete Job Control Workflow

```text
Start a Process
       │
       ▼
Foreground
       │
       ├──────── Ctrl + C ─────────► Process Terminated
       │
       └──────── Ctrl + Z ─────────► Process Suspended
                                         │
                                         ├──── fg %job ───► Foreground
                                         │
                                         ├──── bg %job ───► Background
                                         │
                                         └──── kill %job ─► Terminated

Background
       │
       ├──── fg %job ───────────────► Foreground
       ├──── kill %job ─────────────► Terminated
       └──── Continues Running While Terminal Remains Available
```

---

# 🧪 Practice Exercises

## Exercise 1 – Create a Foreground Loop

Execute:

```bash
while true; do echo "Process One" >> /tmp/jobs.log; sleep 1; done
```

Observe that:

- The terminal becomes occupied.
- The shell prompt is unavailable.

---

## Exercise 2 – Monitor the File

From a second terminal, execute:

```bash
tail -f /tmp/jobs.log
```

Observe that the file updates every second.

---

## Exercise 3 – Terminate the Loop

Press:

```text
Ctrl + C
```

Verify that the file is no longer updating.

---

## Exercise 4 – Suspend the Loop

Start the loop again and press:

```text
Ctrl + Z
```

Display the current jobs:

```bash
jobs
```

---

## Exercise 5 – Resume in the Foreground

Execute:

```bash
fg %1
```

The suspended job resumes in the foreground.

---

## Exercise 6 – Resume in the Background

Suspend the foreground process:

```text
Ctrl + Z
```

Then execute:

```bash
bg %1
```

The process resumes in the background.

---

## Exercise 7 – Start a Second Job

Execute:

```bash
while true; do echo "Process Two" >> /tmp/jobs.log; sleep 2; done
```

Suspend it:

```text
Ctrl + Z
```

Display all jobs:

```bash
jobs
```

---

## Exercise 8 – Run Two Jobs Simultaneously

Resume the first job in the background:

```bash
bg %1
```

Resume the second job in the foreground:

```bash
fg %2
```

Observe that both processes continue executing simultaneously.

---

## Exercise 9 – Display Process IDs

Display jobs together with their Process IDs:

```bash
jobs -l
```

Observe the relationship between:

- Job Numbers
- Process IDs (PIDs)

---

## Exercise 10 – Terminate All Jobs

Terminate every job:

```bash
kill $(jobs -p)
```

Verify:

```bash
jobs
```

No active jobs should remain.

---

# 🔧 Troubleshooting Scenarios

## Scenario 1 – The Terminal Is Occupied

A long-running foreground process is preventing you from entering new commands.

Solution:

Press:

```text
Ctrl + Z
```

Resume the process in the background:

```bash
bg
```

The terminal becomes available immediately.

---

## Scenario 2 – A Process Was Accidentally Suspended

Display the current jobs:

```bash
jobs
```

Resume the job in the foreground:

```bash
fg %1
```

Or resume it in the background:

```bash
bg %1
```

---

## Scenario 3 – The File Is No Longer Updating

Check the job status:

```bash
jobs
```

If the job status is:

```text
Stopped
```

Resume it:

```bash
bg %job_number
```

---

## Scenario 4 – Multiple Processes Are Writing to the Same File

Display all jobs:

```bash
jobs -l
```

Terminate the unnecessary process:

```bash
kill %job_number
```

This prevents multiple processes from writing to the same file simultaneously.

---

## Scenario 5 – "No Such Job" Error

If:

```bash
fg %1
```

returns:

```text
fg: %1: no such job
```

Possible reasons:

- The job has already completed.
- The job was terminated.
- The job belongs to another shell session.

Remember:

Job numbers exist **only within the shell session where the job was started**.

---

## Scenario 6 – The Process Should Continue After Logout

Normal background jobs terminate when the shell session ends.

To keep a process running after logout, use:

```bash
nohup command > output.log 2>&1 &
```

Alternatively, use one of the following tools:

- `screen`
- `tmux`
- `systemd`

These utilities allow processes to continue running independently of the user's terminal session.

---

# 📌 Quick Revision

| Action | Command or Shortcut |
|----------|--------------------|
| Run in the foreground | `command` |
| Run directly in the background | `command &` |
| Terminate a foreground process | `Ctrl + C` |
| Suspend a foreground process | `Ctrl + Z` |
| Display shell jobs | `jobs` |
| Display jobs with PIDs | `jobs -l` |
| Resume Job 1 in the foreground | `fg %1` |
| Resume Job 1 in the background | `bg %1` |
| Terminate Job 1 | `kill %1` |
| Terminate all shell jobs | `kill $(jobs -p)` |
| Monitor a file | `tail -f file` |

---

# 📖 Key Takeaways

- A foreground process occupies the terminal.
- `Ctrl + C` terminates a foreground process.
- `Ctrl + Z` suspends a foreground process without terminating it.
- The `jobs` command displays all jobs associated with the current shell.
- `fg` moves a job to the foreground.
- `bg` resumes a stopped job in the background.
- Background jobs allow you to continue using the terminal while they execute.
- Multiple jobs can run simultaneously.
- Multiple processes can write to the same file, causing it to update more rapidly.
- Job numbers (such as `%1`) are managed by the shell.
- Process IDs (PIDs) are assigned and managed by the Linux Kernel.

---

# 💡 Remember

> **Think of Foreground and Background Jobs as workers sharing a desk.**
>
> - A **Foreground Job** occupies your desk, so you cannot use it until the task is complete.
> - **Ctrl + Z** temporarily pauses the worker.
> - **bg** sends the worker to continue the task in the background.
> - **fg** brings the worker back to your desk.
> - **Ctrl + C** ends the worker's task completely.
>
> Understanding Linux Job Control allows you to efficiently manage multiple tasks without interrupting your workflow.

---

## Golden Job Control Workflow

```text
Foreground
     │
     ├──────── Ctrl + C ───────► Terminated
     │
     └──────── Ctrl + Z ───────► Suspended
                                     │
                                     ├──── bg ───► Background
                                     │
                                     └──── fg ───► Foreground
```

---

# ✅ Lab Summary

In this lab, you learned how to:

- Create continuous processes using a `while` loop.
- Run processes in both the foreground and background.
- Suspend and resume jobs using `Ctrl + Z`, `fg`, and `bg`.
- Monitor jobs using the `jobs` command.
- Understand the difference between Job Numbers and Process IDs.
- Control multiple jobs simultaneously.
- Terminate individual jobs or all jobs in the current shell.
- Troubleshoot common job control scenarios encountered by Linux administrators.

Mastering Linux Job Control is an essential skill for every Linux System Administrator and RHCSA candidate.