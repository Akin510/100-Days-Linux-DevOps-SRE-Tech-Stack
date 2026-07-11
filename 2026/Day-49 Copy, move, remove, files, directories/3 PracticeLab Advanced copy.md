# MODULE 02/07 – Practice Lab: Advanced Copy Operations with `cp`
> **Hands-on Practice Lab – Copying Multiple Files and Directories Using the `cp` Command**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Copy multiple files using a single command.
- Use both **Relative Path** and **Absolute Path** while copying files.
- Copy an entire directory recursively.
- Understand the purpose of the `cp -R` option.
- Verify directory contents using various `ls` command options.
- View command documentation using the `man` command.

---

# 📖 Introduction

In the previous practice lab, we learned how to copy a single file using the **`cp` command**.

In this lab, we will learn how to:

- Copy multiple files with a single command.
- Use Relative and Absolute paths.
- Copy entire directories.
- Use advanced options of the `ls` command.

These commands are widely used in RHCSA exams and in day-to-day Linux system administration.

---

# 🔬 Lab 1 – Verify Your Current Location

First, check your current working directory.

```bash
pwd
```

Display the contents of the current directory.

```bash
ls
```

Assume the current directory contains the following:

```text
blockbuster1.ogg
blockbuster2.ogg
blockbuster3.ogg
projectX
projectY
projectZ
```

---

# 🔬 Lab 2 – Copy Multiple Files Using a Relative Path

Now copy all three files into the **projectZ** directory using a single command.

Command:

```bash
cp blockbuster1.ogg blockbuster2.ogg blockbuster3.ogg projectZ/
```

### Explanation

| Part | Meaning |
|------|---------|
| `cp` | Copy command |
| `blockbuster1.ogg` | Source file |
| `blockbuster2.ogg` | Source file |
| `blockbuster3.ogg` | Source file |
| `projectZ/` | Destination directory |

This command copies all three files into the **projectZ** directory.

---

# 🔬 Lab 3 – Verify the Copy

Move into the destination directory.

```bash
cd projectZ
```

Verify the copied files.

```bash
ls
```

Expected Output:

```text
blockbuster1.ogg
blockbuster2.ogg
blockbuster3.ogg
```

Return to the previous directory.

```bash
cd ..
```

---

# 🔬 Lab 4 – Copy Multiple Files Using an Absolute Path

Now copy the same files into the **projectX** directory using an **Absolute Path**.

Command:

```bash
cp blockbuster1.ogg blockbuster2.ogg blockbuster3.ogg /home/dev1/projectX/
```

Here, the complete path is specified.

This is called an **Absolute Path**.

---

# Relative Path vs Absolute Path

## Relative Path

A relative path starts from the current working directory.

Example:

```bash
cp blockbuster1.ogg blockbuster2.ogg blockbuster3.ogg projectZ/
```

---

## Absolute Path

An absolute path starts from the root directory (`/`).

Example:

```bash
cp blockbuster1.ogg blockbuster2.ogg blockbuster3.ogg /home/dev1/projectX/
```

Both methods are correct.

---

# 🔬 Lab 5 – Copy an Entire Directory

Now let's copy an entire directory.

Suppose you want to copy:

```text
projectX
```

to a new directory named:

```text
projectK
```

Command:

```bash
cp projectX projectK
```

Expected Output:

```text
cp: -r not specified; omitting directory 'projectX'
```

This error occurs because **the `cp` command does not copy directories by default.**

---

# Using the `-R` Option

To copy directories recursively, use:

```bash
cp -R projectX projectK
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-R` | Recursive Copy |

This command:

- Creates a new directory named **projectK**.
- Copies all files and subdirectories from **projectX** into **projectK**.

---

# 🔬 Lab 6 – Verify the Directory Copy

Display the contents of the copied directory.

```bash
ls -la projectK
```

Expected Output:

```text
blockbuster1.ogg
blockbuster2.ogg
blockbuster3.ogg
```

This confirms that the directory was copied successfully.

---

# 🔬 Lab 7 – View the Contents of a Directory

To display detailed information about any directory:

```bash
ls -la projectX
```

or

```bash
ls -la projectK
```

This command displays:

- Files
- Hidden files
- Permissions
- Ownership
- File size
- Date and time

---

# 🔬 Lab 8 – Verify Nested Directories

Suppose you have the following structure:

```text
project
└── app
    └── guacamole
```

Display the contents of the **project** directory.

```bash
ls -la project
```

Output:

```text
app
```

Display the contents of the **app** directory.

```bash
ls -la project/app
```

Output:

```text
guacamole
```

---

# 🔬 Lab 9 – View Command Documentation

To view complete documentation for any command:

```bash
man ls
```

or

```bash
man cp
```

The manual page provides:

- Syntax
- Options
- Examples
- Description

---

# 🔬 Lab 10 – Recursive Directory Listing

To display all directories and their contents recursively:

```bash
ls -lR
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-l` | Long listing format |
| `-R` | Recursive listing |

If the output is very long, use:

```bash
ls -lR | less
```

The `less` command displays the output one page at a time.

---

# Directory Structure

After completing the lab, your directory structure should look similar to this:

```text
/home/dev1
│
├── blockbuster1.ogg
├── blockbuster2.ogg
├── blockbuster3.ogg
├── projectX
│   ├── blockbuster1.ogg
│   ├── blockbuster2.ogg
│   └── blockbuster3.ogg
├── projectK
│   ├── blockbuster1.ogg
│   ├── blockbuster2.ogg
│   └── blockbuster3.ogg
├── projectY
└── projectZ
    ├── blockbuster1.ogg
    ├── blockbuster2.ogg
    └── blockbuster3.ogg
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Create three files.

```bash
touch file1 file2 file3
```

---

## Exercise 2

Copy all three files into a backup directory.

```bash
cp file1 file2 file3 backup/
```

---

## Exercise 3

Recursively copy the backup directory.

```bash
cp -R backup backup_copy
```

---

## Exercise 4

Verify the copied directory.

```bash
ls -la backup_copy
```

---

## Exercise 5

Display a recursive listing.

```bash
ls -lR | less
```

---

## Exercise 6

View the documentation for the `cp` command.

```bash
man cp
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

An error appears while copying a directory.

Command:

```bash
cp projectX projectK
```

Error:

```text
cp: -r not specified; omitting directory
```

Solution:

```bash
cp -R projectX projectK
```

---

### Scenario 2

You want to display all directories and their contents.

Command:

```bash
ls -lR
```

---

### Scenario 3

The output is too long.

Command:

```bash
ls -lR | less
```

---

### Scenario 4

You cannot remember the options for a command.

Commands:

```bash
man cp
```

or

```bash
man ls
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `cp file1 file2 dir/` | Copy multiple files |
| `cp -R dir1 dir2` | Copy a directory recursively |
| `ls -la directory` | Display detailed directory contents |
| `ls -lR` | Display a recursive directory listing |
| `ls -lR \| less` | View recursive output page by page |
| `man cp` | Display the `cp` command manual |
| `man ls` | Display the `ls` command manual |

---

# 📖 Key Takeaways

- The `cp` command can copy multiple files using a single command.
- Both Relative and Absolute paths can be used.
- The **`-R`** option is required to copy directories recursively.
- `ls -la` displays detailed directory information.
- `ls -lR` displays all directories and files recursively.
- The `man` command provides complete documentation for Linux commands.

---

# 💡 Remember

> **Think of the `cp` command as a photocopy machine.**
>
> - It can create a copy of a single file.
> - It can copy multiple files in one command.
> - It can copy an entire directory when used with the `-R` option.
>
> **Golden Rule:**
>
> ```text
> Copy Files      = cp
> Copy Directory  = cp -R
> ```
>
> **Never forget to use the `-R` option when copying directories!**