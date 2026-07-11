# MODULE 08 – Monitor and Manage Linux Processes
> **Introduction to Linux Processes**

> DAY 65
> JULY 11TH, 2026

---

# 🎯 Learning Objectives

In this module, you will learn:

- What is a Linux Process?
- How a process is created.
- Process Life Cycle.
- Parent and Child Processes.
- Process Forking.
- Process IDs (PID & PPID).
- Process Environment.
- Process States.
- Why process management is important for Linux System Administrators.

---

# 📖 Introduction

Every task performed on a Linux system runs as a **process**.

Whenever you execute a command, start a program, or run a script, Linux creates one or more processes to perform the requested task.

The Linux **Kernel** is responsible for creating, scheduling, managing, and terminating processes.

Without processes, Linux cannot perform any work.

---

# 1. What is a Process?

A **process** is:

> **An instance of a running program.**

Or

> **A running instance of an executable program that has been launched by the operating system.**

Simply put:

- Program stored on disk → **Static**
- Program running in memory → **Process**

---

# Process Example

Suppose you execute:

```bash
pwd
```

What happens behind the scenes?

1. Bash receives the command.
2. Linux creates a new process.
3. The Kernel allocates:
   - CPU time
   - Memory
4. The process executes the `pwd` command.
5. The current working directory is displayed.
6. The process exits.

---

# Another Example

Suppose you execute a backup script.

```bash
./backup.sh
```

Linux performs the following:

- Creates a process.
- Allocates CPU.
- Allocates RAM.
- Executes the script.
- Creates child processes if needed.
- Finishes execution.
- Releases allocated resources.

---

# Definition of a Process

A process is simply:

> **A set of executable instructions loaded into memory and executed by the CPU.**

Every process receives system resources from the Linux Kernel.

---

# 📊 Process Life Cycle Diagram

The following diagram illustrates how a Linux process is created, executes, creates child processes, and eventually exits.

![Linux Process Life Cycle](/mnt/data/image(523).png)

### Diagram Explanation

1. A **process** starts execution.
2. It may create a **child process** using the **fork()** system call.
3. The child process may execute a different program using **exec()**.
4. When execution finishes, the child process **exits**.
5. Until the parent collects its exit status, the child temporarily becomes a **Zombie Process**.
6. Once the parent acknowledges it, the zombie entry is removed.

---

# 2. Components of a Process

Every process consists of several important components.

## Memory Space

Each process receives its own allocated memory.

This memory stores:

- Program code
- Variables
- Stack
- Heap

---

## Security Information

Every process has:

- Owner (User)
- Group
- Permissions
- Privileges

These determine what the process is allowed to do.

---

## Execution Threads

Every process contains one or more execution threads.

These threads execute the program instructions.

---

## Process State

Every process is always in a particular state.

Examples:

- Running
- Sleeping
- Waiting
- Stopped
- Zombie

We will study these states in detail later.

---

# 3. Process Environment

Every process also carries information known as its **Environment**.

This includes:

- Local Variables
- Environment Variables
- Current Working Directory
- Open Files
- File Descriptors
- Network Ports
- Allocated Resources

These resources remain associated with the process while it is running.

---

# 4. Process Creation (Fork)

Linux creates new processes using a system call called:

```text
fork()
```

The **fork()** system call duplicates the parent process.

The newly created process is called the:

> **Child Process**

The original process is called the:

> **Parent Process**

---

# Parent and Child Process

Example:

```text
Parent Process
        │
     fork()
        │
        ▼
Child Process
```

The child process initially inherits most of the parent's properties.

---

# 5. What Does the Child Process Inherit?

When a child process is created, it inherits many attributes from its parent.

These include:

- Security Identity
- User ID
- Group ID
- File Descriptors
- Environment Variables
- Resource Limits
- Program Code
- Working Directory

After creation, the child process may execute its own code independently.

---

# 6. Process IDs (PID)

Every process receives a unique identifier called:

> **PID (Process ID)**

Example:

```text
PID = 3521
```

Linux uses the PID to:

- Track processes
- Manage resources
- Send signals
- Perform security checks

---

# Parent Process ID (PPID)

Every child process also stores:

> **PPID (Parent Process ID)**

Example:

```text
Parent Process
PID = 1000

Child Process
PID = 1100
PPID = 1000
```

---

# 7. Any Process Can Create Child Processes

Every running process can create one or more child processes.

Example:

```text
Process A
    │
    ├── Process B
    │
    ├── Process C
    │
    └── Process D
```

Each child process may itself create additional child processes.

---

# 8. What Happens if the Parent Process Terminates?

Normally:

If the parent process terminates,

its child processes are also terminated or re-parented depending on the situation and the operating system.

Linux reassigns orphaned processes to **systemd (PID 1)** on modern systems.

---

# 9. The First Process on Linux

On modern Red Hat-based Linux systems:

The very first process started during boot is:

```text
systemd
```

It has:

```text
PID = 1
```

Every process running on the system ultimately descends from **systemd**.

---

# 10. Parent Process Sleeping

Often, a parent process waits while its child executes.

Example:

```text
Parent Process
      │
      ▼
Sleep (Waiting)
      │
      ▼
Child Executes
```

The parent remains in a waiting state until the child finishes.

---

# 11. Child Process Completion

When the child finishes:

- Resources are released.
- Memory is cleaned.
- Open files are closed.
- CPU resources are freed.

Before disappearing completely, the process briefly becomes a:

> **Zombie Process**

---

# 12. Zombie Process

A Zombie Process is:

> **A process that has completed execution but still has an entry in the process table until its parent reads its exit status.**

Zombie processes consume almost no system resources.

They only occupy an entry in the process table.

---

# 13. Why Are Processes Important?

Linux processes allow the operating system to:

- Run multiple programs simultaneously.
- Manage CPU scheduling.
- Allocate memory efficiently.
- Isolate applications.
- Improve multitasking.
- Provide security.

---

# Real-World Example

Suppose you run:

```bash
firefox
```

Linux creates a Firefox process.

Firefox may create many child processes such as:

- Rendering Engine
- GPU Process
- Network Process
- Audio Process

Each child performs a separate task.

---

# Process Creation Flow

```text
User Runs Command
        │
        ▼
Kernel Creates Process
        │
        ▼
CPU & Memory Allocated
        │
        ▼
Program Executes
        │
        ▼
Child Processes Created (if needed)
        │
        ▼
Execution Completed
        │
        ▼
Resources Released
        │
        ▼
Zombie Entry Removed
```

---

# 📌 Quick Revision

| Term | Description |
|------|-------------|
| Process | Running instance of a program |
| PID | Process ID |
| PPID | Parent Process ID |
| Parent Process | Original process |
| Child Process | Created using `fork()` |
| fork() | Creates a child process |
| exec() | Replaces the process image with a new program |
| Zombie | Process that has exited but still has a process table entry |
| systemd | First Linux process (PID 1) |

---

# 📖 Key Takeaways

- Every running program is a process.
- Every process receives CPU and memory from the Kernel.
- Every process has a unique PID.
- Child processes are created using `fork()`.
- Child processes inherit many properties from their parent.
- Parents often wait while children execute.
- Finished processes briefly become Zombie processes.
- All Linux processes ultimately originate from **systemd (PID 1)**.

---

# 💡 Remember

> **Think of a Process as an employee in a company.**
>
> - The **Kernel** is the Manager.
> - The **Program** is the Job Description.
> - The **Process** is the Employee doing the work.
> - The **fork()** system call hires a new employee (Child Process).
> - The **exec()** system call gives that employee a new job.
> - When the work finishes, the employee exits.
> - Until HR (the Parent Process) completes the paperwork, the employee briefly appears as a **Zombie Process**.
>
> **Golden Rule:**
>
> ```text
> Program on Disk
>        │
>        ▼
> Running in Memory
>        │
>        ▼
> Process
> ```