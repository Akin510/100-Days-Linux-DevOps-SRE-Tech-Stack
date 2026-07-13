# MODULE 11 – Constructing Pipelines
> **Understanding Pipelines in Linux**

---

# 🎯 Learning Objectives

By the end of this lesson, you will be able to:

- Understand what a pipeline is in Linux.
- Learn how the pipe (`|`) operator works.
- Understand Standard Output (stdout) and Standard Input (stdin).
- Combine multiple Linux commands using pipelines.
- Filter and process command output efficiently.
- Redirect pipeline output to a file.

---

# 📖 Introduction

One of the most powerful features of the Linux command line is the **pipeline**.

A **pipeline** allows you to connect two or more commands together so that the output of one command becomes the input of the next command.

Instead of manually saving output and running another command on it, Linux allows commands to communicate directly using the **pipe operator (`|`)**.

This makes command execution faster, cleaner, and more efficient.

---

# What is a Pipeline?

A **pipeline** is a sequence of one or more commands separated by the **vertical bar (`|`)**, also known as the **pipe**.

General Syntax:

```bash
command1 | command2 | command3
```

The output of the first command becomes the input of the second command.

The output of the second command becomes the input of the third command.

This process continues until the last command is executed.

---

# How a Pipeline Works

```text
Command 1
     │
     ▼
 Standard Output (stdout)
     │
     ▼
     Pipe (|)
     │
     ▼
 Standard Input (stdin)
     │
     ▼
Command 2
```

Instead of displaying the output on the screen immediately, Linux passes it directly to the next command.

---

# Understanding stdout and stdin

Every Linux command has three standard data streams:

| Stream | Description |
|---------|-------------|
| Standard Input (stdin) | Receives input for a command |
| Standard Output (stdout) | Displays normal command output |
| Standard Error (stderr) | Displays error messages |

In a pipeline:

- The **stdout** of one command becomes the **stdin** of the next command.

---

# Why Use Pipelines?

Pipelines allow you to:

- Combine multiple commands.
- Filter large amounts of data.
- Search output.
- Count results.
- Display output page by page.
- Save processed output to files.

Instead of running several commands separately, you can perform all operations in one command line.

---

# Example 1 – Count Files

Suppose you execute:

```bash
ls
```

This command displays the files in the current directory.

Now suppose you want to know **how many entries** are displayed.

You can combine the commands:

```bash
ls | wc -l
```

---

# How It Works

```text
ls
 │
 ▼
List of Files
 │
 ▼
Pipe (|)
 │
 ▼
wc -l
 │
 ▼
Number of Lines
```

The `wc -l` command counts the number of lines received from `ls`.

Example Output:

```text
15
```

---

# Example 2 – Display Long Directory Listing

Display all files inside:

```bash
ls -l /usr/bin
```

This command produces a very long output.

Often, the first few lines scroll off the screen before you can read them.

---

# View Output Page by Page

Instead of displaying everything at once, use:

```bash
ls -l /usr/bin | less
```

Now the output opens inside the **less** pager.

You can:

- Press **Enter** to move down one line.
- Press **Space** to move down one page.
- Press **b** to move back one page.
- Press **q** to quit.

---

# Pipeline Flow

```text
ls -l /usr/bin
        │
        ▼
Standard Output
        │
        ▼
       Pipe
        │
        ▼
      less
```

The output from `ls` becomes the input for `less`.

---

# Example 3 – Display Only the First Five Lines

Suppose you execute:

```bash
ls -ltr
```

By default, many lines are displayed.

If you only want the first five lines:

```bash
ls -ltr | head -n 5
```

---

# How It Works

```text
ls -ltr
     │
     ▼
Directory Listing
     │
     ▼
Pipe
     │
     ▼
head -n 5
     │
     ▼
First Five Lines
```

Example Output:

```text
total 20
-rw-r--r-- file1
-rw-r--r-- file2
drwxr-xr-x dir1
drwxr-xr-x dir2
```

---

# Example 4 – Save Pipeline Output to a File

You can redirect the final output of a pipeline into a file.

Example:

```bash
ls -ltr | head -n 5 > output.txt
```

Now the first five lines are written into:

```text
output.txt
```

Display the file:

```bash
cat output.txt
```

Example Output:

```text
total 20
-rw-r--r-- file1
-rw-r--r-- file2
drwxr-xr-x dir1
drwxr-xr-x dir2
```

---

# Pipeline with Output Redirection

```text
ls -ltr
     │
     ▼
Pipe
     │
     ▼
head -n 5
     │
     ▼
Output Redirection (>)
     │
     ▼
output.txt
```

This combines both **pipelines** and **output redirection**.

---

# Advantages of Pipelines

Pipelines provide several advantages:

- Reduce manual work.
- Eliminate temporary files.
- Improve efficiency.
- Combine powerful commands.
- Simplify data processing.
- Allow flexible command chaining.

---

# Common Pipeline Commands

| Command | Purpose |
|----------|---------|
| `ls` | List files |
| `cat` | Display file contents |
| `grep` | Search text |
| `head` | Display first lines |
| `tail` | Display last lines |
| `sort` | Sort output |
| `uniq` | Remove duplicate lines |
| `wc` | Count lines, words, or characters |
| `less` | View output page by page |

---

# Example Pipeline Combinations

Count files:

```bash
ls | wc -l
```

Search for a process:

```bash
ps -ef | grep ssh
```

Display only the first ten users:

```bash
cat /etc/passwd | head
```

Display the last five log entries:

```bash
cat /var/log/messages | tail -n 5
```

Sort usernames:

```bash
cat users.txt | sort
```

---

# Practice Exercises

## Exercise 1

List files and count them:

```bash
ls | wc -l
```

---

## Exercise 2

Display `/usr/bin` page by page:

```bash
ls -l /usr/bin | less
```

---

## Exercise 3

Display the first five files:

```bash
ls -ltr | head -n 5
```

---

## Exercise 4

Display the last five files:

```bash
ls -ltr | tail -n 5
```

---

## Exercise 5

Save the first five lines into a file:

```bash
ls -ltr | head -n 5 > files.txt
```

Verify:

```bash
cat files.txt
```

---

# 🔧 Troubleshooting

### Problem

No output appears.

Check whether the first command is producing any output.

Example:

```bash
ls
```

---

### Problem

The second command behaves unexpectedly.

Verify that it accepts input from **stdin**.

---

### Problem

Output is too large.

Use:

```bash
| less
```

to browse the output page by page.

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `|` | Connects commands together |
| `ls | wc -l` | Counts directory entries |
| `ls -l /usr/bin | less` | Displays output page by page |
| `head -n 5` | Displays the first five lines |
| `tail -n 5` | Displays the last five lines |
| `>` | Redirects output to a file |

---

# 📖 Key Takeaways

- A pipeline connects two or more Linux commands.
- The pipe operator is represented by `|`.
- The **stdout** of one command becomes the **stdin** of the next command.
- Pipelines allow commands to work together efficiently.
- Pipelines reduce manual work and simplify command execution.
- Pipelines can be combined with output redirection to save results.

---

# 💡 Remember

> **Think of a pipeline like an assembly line in a factory.**
>
> - The first worker completes one task.
> - The finished work is immediately passed to the second worker.
> - The second worker processes it and passes it to the next.
> - Each command performs one job, and the pipe (`|`) passes the result to the next command.
>
> **Golden Rule:**
>
> ```text
> Command 1
>      │
>      ▼
> Standard Output
>      │
>      ▼
> Pipe (|)
>      │
>      ▼
> Standard Input
>      │
>      ▼
> Command 2
> ```
>
> Pipelines allow multiple Linux commands to work together efficiently, making complex tasks simple and powerful.