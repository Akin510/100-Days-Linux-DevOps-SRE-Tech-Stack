# MODULE 11 – Practice Lab: Using the `tee` Command in Linux
> **Hands-on Practice Lab – Displaying and Saving Output Simultaneously**

---

# 🎯 Lab Objectives

By the end of this lab, you will be able to:

- Understand the purpose of the `tee` command.
- Learn how `tee` works with pipelines.
- Save command output to a file while displaying it on the screen.
- Append output to an existing file using `tee -a`.
- Combine `tee` with other Linux commands and pipelines.

---

# 📖 Introduction

In the previous lesson, we learned how to use **pipelines** to connect multiple Linux commands.

Sometimes, you may want to:

- Display the output on the terminal.
- Save the same output to a file at the same time.

Normally, if you use output redirection (`>`), the output is written to a file but **is not displayed on the screen**.

The **`tee` command** solves this problem.

It allows you to **display the output on the terminal and save it to a file simultaneously**.

---

# What is the `tee` Command?

The `tee` command reads data from **Standard Input (stdin)** and performs two actions at the same time:

1. Displays the output on the screen.
2. Writes the same output to one or more files.

General Syntax:

```bash
command | tee filename
```

---

# How the `tee` Command Works

```text
Command
    │
    ▼
 Standard Output
    │
    ▼
   Pipe (|)
    │
    ▼
     tee
   ┌───────┐
   │       │
   ▼       ▼
Screen   File
```

The output is duplicated:

- One copy goes to the terminal.
- One copy is written to the specified file.

---

# Why Use `tee`?

Without `tee`:

```bash
command > output.txt
```

Result:

- Output is saved to the file.
- Nothing is displayed on the screen.

---

With `tee`:

```bash
command | tee output.txt
```

Result:

- Output is displayed on the screen.
- The same output is saved to the file.

---

# Example 1 – Redirect Output Without `tee`

Suppose you want to display only the first five lines of a directory listing and save the result.

Command:

```bash
ls -ltr | head -n 5 > files.txt
```

Result:

- Output is written to `files.txt`.
- Nothing is displayed on the terminal.

Display the file:

```bash
cat files.txt
```

---

# Example 2 – Display and Save Output Using `tee`

Now perform the same task using `tee`:

```bash
ls -ltr | head -n 5 | tee files.txt
```

Result:

- The first five lines are displayed on the screen.
- The same output is saved to `files.txt`.

This is the main advantage of the `tee` command.

---

# Pipeline Flow

```text
ls -ltr
     │
     ▼
head -n 5
     │
     ▼
tee files.txt
   ┌───────┐
   │       │
   ▼       ▼
Screen   files.txt
```

---

# Verify the File

Display the saved file:

```bash
cat files.txt
```

The contents of the file will match the output shown on the screen.

---

# Example 3 – Save the Current Date

Suppose you execute:

```bash
date
```

Normally, the date appears only on the screen.

To display it and save it simultaneously:

```bash
date | tee date.txt
```

Example Output:

```text
Tue Jul 14 10:25:30 AM UTC 2026
```

The output appears:

- On the screen.
- Inside `date.txt`.

Verify:

```bash
cat date.txt
```

---

# Example 4 – Append Output

By default, `tee` overwrites the destination file.

Suppose you execute:

```bash
date | tee date.txt
```

The previous contents of `date.txt` are replaced.

If you want to **append** instead of overwrite, use the `-a` option.

Syntax:

```bash
command | tee -a filename
```

Example:

```bash
date | tee -a date.txt
```

Now each execution adds a new line to the end of the file.

---

# Append Flow

```text
Command
    │
    ▼
   tee -a
    │
    ▼
Existing File
    │
    ▼
New Output Added
```

Nothing in the existing file is removed.

---

# Verify the Appended File

Display the file:

```bash
cat date.txt
```

Example Output:

```text
Tue Jul 14 10:20:00 AM UTC 2026
Tue Jul 14 10:25:30 AM UTC 2026
Tue Jul 14 10:30:15 AM UTC 2026
```

Each new execution is appended to the file.

---

# `tee` vs Output Redirection

| Method | Display on Screen | Save to File |
|----------|------------------|--------------|
| `>` | No | Yes |
| `>>` | No | Yes (Append) |
| `tee` | Yes | Yes |
| `tee -a` | Yes | Yes (Append) |

---

# Common Examples

Display and save directory listing:

```bash
ls -ltr | tee files.txt
```

---

Display and save the first five lines:

```bash
ls -ltr | head -n 5 | tee top5.txt
```

---

Display and save the current date:

```bash
date | tee date.txt
```

---

Append the current date:

```bash
date | tee -a date.txt
```

---

Display running processes and save them:

```bash
ps -ef | tee processes.txt
```

---

Search for SSH processes and save the results:

```bash
ps -ef | grep ssh | tee ssh-processes.txt
```

---

# Practice Exercises

## Exercise 1

Display the current date and save it:

```bash
date | tee today.txt
```

---

## Exercise 2

Append the current date:

```bash
date | tee -a today.txt
```

---

## Exercise 3

Save the first five files:

```bash
ls -ltr | head -n 5 | tee files.txt
```

---

## Exercise 4

Save all running processes:

```bash
ps -ef | tee processes.txt
```

---

## Exercise 5

Search for SSH processes and save them:

```bash
ps -ef | grep ssh | tee ssh.txt
```

---

# 🔧 Troubleshooting

### Problem

The output is not appearing on the screen.

Verify that you are using:

```bash
tee
```

instead of:

```bash
>
```

---

### Problem

The file is overwritten.

Use:

```bash
tee -a
```

instead of:

```bash
tee
```

---

### Problem

The file is empty.

Ensure that the first command in the pipeline is producing output.

Example:

```bash
ls
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `tee file.txt` | Display output and save it to a file |
| `tee -a file.txt` | Display output and append it to a file |
| `>` | Redirect output to a file |
| `>>` | Append output to a file |
| `cat file.txt` | Display the contents of a file |

---

# 📖 Key Takeaways

- The `tee` command reads input from **stdin**.
- It displays the output on the screen while simultaneously saving it to a file.
- `tee` is commonly used with pipelines.
- `tee -a` appends output instead of overwriting the file.
- The `tee` command is useful for logging command output while monitoring it in real time.

---

# 💡 Remember

> **Think of the `tee` command like a road junction.**
>
> - A single road (the command output) reaches the junction.
> - One road continues to the screen.
> - The other road goes to a file.
> - Both destinations receive the same information at the same time.
>
> **Golden Rule:**
>
> ```text
> Command
>     │
>     ▼
>   Pipe (|)
>     │
>     ▼
>    tee
>   ┌───────┐
>   │       │
>   ▼       ▼
> Screen   File
> ```
>
> The `tee` command allows you to **view command output live while simultaneously saving it for future reference**.