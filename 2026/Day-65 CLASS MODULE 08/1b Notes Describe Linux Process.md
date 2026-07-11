# MODULE 08 – Describing a Process (How It Works)
> **Understanding Linux Process States and the Process Life Cycle**

---

# 🎯 Learning Objectives

In this lesson, you will learn:

- What a Linux process is.
- How a process moves through different states during execution.
- The Linux Process Life Cycle.
- The responsibilities of the Linux Kernel in process management.
- New, Ready, Running, Waiting, Exit, and Zombie states.
- How CPU scheduling works.
- Why understanding process states is important for Linux administrators.

---

# 📖 Introduction

A **process** is simply an **instance of a running program**.

Another way to define a process is:

> **A set of executable instructions loaded into memory and executed by the CPU.**

During its lifetime, every process moves through several different states before it completes execution.

Understanding these states is essential for troubleshooting Linux systems and understanding how the operating system manages workloads.

The **Linux Kernel** is responsible for managing every process on the system. It keeps track of all running processes, schedules CPU time, allocates resources, and controls the transitions between different process states.

---

# Linux Process Life Cycle

<img src="../../.github/assets/Process States.png" width="700">

Every Linux process typically moves through the following states:

```text
           fork()
             │
             ▼
         New State
             │
             ▼
        Ready State
             │
             ▼
       Running State
        /          \
       /            \
Waiting/Sleep     Exit
       │             │
       ▼             ▼
    Running       Zombie
```

---

# 1. New State

The **New State** is the first state in the life cycle of a process.

A process enters this state immediately after it is created.

During this stage, the Kernel:

- Creates the process.
- Assigns a Process ID (PID).
- Allocates memory.
- Initializes system resources.

At this point, the process has not yet started executing.

---

# 2. Ready State

After being created, the process moves into the **Ready State**.

A process in the Ready State:

- Has all the resources required for execution.
- Is waiting for CPU time.
- Is ready to execute as soon as the scheduler selects it.

Multiple processes can exist in the Ready Queue simultaneously.

The Linux Kernel decides which process should execute next based on its **CPU scheduling algorithm** and process priority.

---

# 3. Running State

When the CPU scheduler selects a process from the Ready Queue, it enters the **Running State**.

The CPU begins executing the instructions of the process.

For example, if a user executes:

```bash
pwd
```

Linux creates a process that executes the command.

The process:

- Uses CPU time.
- Reads memory.
- Executes program instructions.
- Produces the required output.

---

# CPU Scheduling

Many processes may be waiting in the Ready Queue.

The Kernel uses a **CPU Scheduling Algorithm** to determine:

- Which process should execute first.
- How long the process should run.
- When another process should receive CPU time.

Processes with higher priority may be selected before lower-priority processes.

---

# 4. Waiting (Blocked) State

While executing, a process may need to wait for another operation to complete.

Common reasons include:

- Reading data from disk.
- Writing data to a file.
- Waiting for network communication.
- Waiting for user input.
- Waiting for a child process to finish.

When this happens, the process enters the:

> **Waiting (Blocked) State**

While the process is waiting, the CPU is assigned to another ready process.

---

# Example: Waiting State

Suppose a process is currently running.

During execution, it creates a child process that begins writing data to the disk.

Since disk operations may take time, the parent process waits for the child process to complete.

The parent process enters the **Waiting (Blocked) State** until the required operation finishes.

Once the disk operation completes, the process returns to the Running State.

---

# Sleeping State

A sleeping process is another form of a waiting process.

Examples include:

- Waiting for I/O operations.
- Waiting for child processes.
- Executing the `sleep` command.
- Waiting for timers to expire.

While sleeping, the process temporarily pauses execution until the required event occurs.

---

# 5. Exit (Termination) State

When a process completes its work, it enters the **Exit State**, also known as the **Termination State**.

At this stage, the Kernel:

- Stops the process.
- Releases allocated memory.
- Closes open files.
- Frees CPU resources.
- Cleans up system resources.

The process has finished execution.

---

# Zombie Process

Sometimes a process completes execution, but its Parent Process has not yet collected its exit status.

During this short period, the process becomes a:

> **Zombie Process**

A Zombie Process:

- Has already completed execution.
- Uses almost no CPU.
- Occupies only an entry in the Process Table.
- Exists until the Parent Process reads its exit status.

Once the Parent Process acknowledges the child's completion, the Zombie entry is removed.

---

# Common Linux Process States

Linux displays several different process states.

| State | Description |
|--------|-------------|
| New | Process has just been created. |
| Ready | Process is waiting for CPU time. |
| Running | Process is currently executing. |
| Waiting / Blocked | Process is waiting for an event or I/O operation. |
| Sleeping | Process is temporarily paused while waiting. |
| Stopped | Process execution has been suspended. |
| Zombie | Process has completed but still has an entry in the process table. |
| Exit | Process has terminated successfully. |

---

# Additional Linux Process State Codes

When viewing processes using commands such as `ps` or `top`, you may encounter additional process state codes.

| Code | Meaning |
|------|---------|
| R | Running or Runnable |
| S | Interruptible Sleep |
| D | Uninterruptible Sleep (usually waiting for I/O) |
| I | Idle Kernel Thread |
| T | Stopped Process |
| t | Traced or Debugged Process |
| Z | Zombie Process |
| X | Dead Process (rarely seen) |

---

# Process State Flow

```text
fork()
   │
   ▼
New
   │
   ▼
Ready
   │
   ▼
Running
   │
   ├─────────────┐
   ▼             │
Waiting/Sleep    │
   │             │
   └─────────────┘
         │
         ▼
      Running
         │
         ▼
       Exit
         │
         ▼
      Zombie
```

---

# Why Understanding Process States Is Important

Linux administrators must understand process states because they help:

- Troubleshoot slow systems.
- Identify blocked processes.
- Detect zombie processes.
- Analyze CPU scheduling.
- Monitor system performance.
- Diagnose I/O bottlenecks.
- Understand application behavior.

---

# 📌 Quick Revision

| State | Purpose |
|--------|---------|
| New | Process is created. |
| Ready | Waiting for CPU time. |
| Running | CPU is executing the process. |
| Waiting | Waiting for I/O or another event. |
| Sleeping | Temporarily paused. |
| Exit | Execution completed. |
| Zombie | Waiting for the parent to collect exit status. |

---

# 📖 Key Takeaways

- Every process moves through multiple states during execution.
- The Linux Kernel manages all process state transitions.
- New processes enter the Ready Queue before execution.
- The CPU Scheduler selects which process runs next.
- Running processes may enter the Waiting or Sleeping state while waiting for resources.
- Completed processes enter the Exit state.
- Zombie processes remain temporarily until the Parent Process collects their exit status.
- Understanding process states is essential for Linux administration and troubleshooting.

---

# 💡 Remember

> **Think of a process as a customer waiting at a bank.**
>
> - **New** → The customer enters the bank.
> - **Ready** → Waiting in line.
> - **Running** → Being served by the teller.
> - **Waiting** → Waiting for documents or approval.
> - **Running Again** → Service resumes.
> - **Exit** → Customer leaves the bank.
> - **Zombie** → The paperwork remains until it is officially filed.
>
> **Golden Rule:**
>
> ```text
> New
>   │
>   ▼
> Ready
>   │
>   ▼
> Running
>   │
>   ▼
> Waiting
>   │
>   ▼
> Running
>   │
>   ▼
> Exit
>   │
>   ▼
> Zombie
> ```