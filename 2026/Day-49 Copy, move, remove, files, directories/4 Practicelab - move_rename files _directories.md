# MODULE 02/07 – Practice Lab: Moving and Renaming Files Using `mv`
> **Hands-on Practice Lab – Move and Rename Files with the `mv` Command**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Rename files using the `mv` command.
- Move files from one directory to another.
- Use both **Relative Path** and **Absolute Path** while moving files.
- Verify moved files using the `ls` command.
- Understand the dual functionality of the `mv` command.

---

# 📖 Introduction

In Linux, the **`mv` (move)** command is used for two important tasks:

1. **Moving files or directories** from one location to another.
2. **Renaming files or directories**.

Unlike the `cp` command, the `mv` command **does not create a copy**. Instead, it moves the original file or directory to a new location or changes its name.

The basic syntax is:

```bash
mv source destination
```

Depending on the destination, the command will either:

- Rename the file.
- Move the file.
- Move and rename the file simultaneously.

---

# What is the `mv` Command?

The `mv` command stands for:

> **Move**

It is used to:

- Move files.
- Move directories.
- Rename files.
- Rename directories.

---

# Basic Syntax

```bash
mv source destination
```

Where:

- **Source** = Existing file or directory.
- **Destination** = New filename or destination directory.

---

# 🔬 Lab 1 – Verify Your Current Location

Check your current working directory.

```bash
pwd
```

List the contents of the directory.

```bash
ls -ltr
```

Assume the following files exist:

```text
newabc
pic1.jpg
pic2.jpg
videos/
```

---

# 🔬 Lab 2 – Rename a File

Suppose you want to rename the file:

```text
newabc
```

to

```text
abc
```

Command:

```bash
mv newabc abc
```

Verify the result.

```bash
ls -ltr
```

Expected Output:

```text
abc
```

The file has been successfully renamed.

---

# Understanding File Renaming

When the destination is a **new filename**, the `mv` command simply renames the file.

Example:

```bash
mv oldname newname
```

No new file is created.

Only the filename changes.

---

# 🔬 Lab 3 – Move a File Using an Absolute Path

Suppose the file:

```text
pic1.jpg
```

is located inside:

```text
/home/dev1/videos/
```

You want to move it to:

```text
/tmp
```

Command:

```bash
mv /home/dev1/videos/pic1.jpg /tmp/
```

Here, the complete path is specified.

This is called an **Absolute Path**.

---

# 🔬 Lab 4 – Verify the File

Move into the `/tmp` directory.

```bash
cd /tmp
```

Display the contents.

```bash
ls -ltr
```

Expected Output:

```text
pic1.jpg
```

This confirms that the file has been moved successfully.

---

# 🔬 Lab 5 – Return to the Previous Directory

Return to your previous working directory.

```bash
cd -
```

or

```bash
cd /home/dev1
```

---

# 🔬 Lab 6 – Move a File Using a Relative Path

Now move:

```text
pic2.jpg
```

to another directory using a **Relative Path**.

Example:

```bash
mv pic2.jpg documents/
```

Since the destination is specified relative to the current directory, this is called a **Relative Path**.

---

# Relative Path vs Absolute Path

## Relative Path

Starts from the current working directory.

Example:

```bash
mv pic2.jpg documents/
```

---

## Absolute Path

Starts from the root directory (`/`).

Example:

```bash
mv /home/dev1/videos/pic1.jpg /tmp/
```

Both methods are correct.

---

# 🔬 Lab 7 – Verify the Move

Navigate to the destination directory.

```bash
cd documents
```

Display the contents.

```bash
ls
```

Expected Output:

```text
pic2.jpg
```

The file has been moved successfully.

---

# Directory Structure

Before moving:

```text
/home/dev1
│
├── abc
├── pic2.jpg
├── videos
│   └── pic1.jpg
└── documents
```

After moving:

```text
/home/dev1
│
├── abc
├── videos
└── documents
    └── pic2.jpg

/tmp
└── pic1.jpg
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Create two files.

```bash
touch file1 file2
```

---

## Exercise 2

Rename `file1` to `linux.txt`.

```bash
mv file1 linux.txt
```

---

## Exercise 3

Create a directory.

```bash
mkdir backup
```

---

## Exercise 4

Move `file2` into the backup directory.

```bash
mv file2 backup/
```

---

## Exercise 5

Verify the move.

```bash
ls backup
```

---

## Exercise 6

Move a file using an absolute path.

Example:

```bash
mv /home/dev1/linux.txt /tmp/
```

---

## Exercise 7

Return to your home directory.

```bash
cd ~
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

The source file does not exist.

Command:

```bash
mv file1 backup/
```

Error:

```text
No such file or directory
```

Verify the filename before running the command.

---

### Scenario 2

The destination directory does not exist.

Command:

```bash
mv file1 storage/
```

Linux will return an error indicating that the destination cannot be found.

---

### Scenario 3

You accidentally renamed the wrong file.

Simply rename it back.

Example:

```bash
mv wrongname correctname
```

---

### Scenario 4

You are unsure whether the file has been moved.

Verify using:

```bash
ls
```

or

```bash
ls -ltr
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `mv old new` | Rename a file |
| `mv file directory/` | Move a file to another directory |
| `mv /path/file /tmp/` | Move a file using an absolute path |
| `mv file documents/` | Move a file using a relative path |
| `pwd` | Display current directory |
| `ls -ltr` | Display directory contents |

---

# 📖 Key Takeaways

- The `mv` command is used to **move** and **rename** files and directories.
- Unlike `cp`, the `mv` command **does not create a duplicate**.
- Relative and Absolute paths can both be used.
- Always verify moved files using the `ls` command.
- The same command can rename a file or move it depending on the destination you specify.

---

# 💡 Remember

> **Think of the `mv` command as physically moving a document from one folder to another.**
>
> - The original file is **not copied**.
> - It simply changes its location or its name.
>
> **Golden Rule:**
>
> ```text
> Rename File = mv oldname newname
>
> Move File = mv file destination/
> ```
>
> **Unlike `cp`, the `mv` command moves the original file instead of creating a copy.**