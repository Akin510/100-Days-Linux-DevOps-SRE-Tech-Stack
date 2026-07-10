# MODULE 02/07 – Practice Lab: Removing Files and Directories
> **Hands-on Practice Lab – Removing Files and Directories Using `rm` and `rmdir`**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Remove files using the `rm` command.
- Remove empty directories using the `rmdir` command.
- Remove directories recursively using the `rm -r` command.
- Forcefully remove files and directories using the `rm -rf` command.
- Understand the difference between `rm` and `rmdir`.
- Learn why the `rm -rf` command should be used with caution.

---

# 📖 Introduction

Managing files and directories also includes removing files that are no longer needed.

Linux provides two commonly used commands for deleting files and directories:

- `rm` – Remove files (and directories with options)
- `rmdir` – Remove empty directories only

By default:

- `rm` removes **files only**.
- `rmdir` removes **empty directories only**.

If a directory contains files or subdirectories, you must use the recursive option (`-r`) with the `rm` command.

---

# What is the `rm` Command?

The `rm` command stands for:

> **Remove**

It is used to delete:

- Files
- Directories (using options)

---

# Basic Syntax

Remove a file:

```bash
rm filename
```

Remove a directory recursively:

```bash
rm -r directory_name
```

Forcefully remove a directory:

```bash
rm -rf directory_name
```

---

# 🔬 Lab 1 – Verify Your Current Location

Check your current working directory.

```bash
pwd
```

List the contents.

```bash
ls -ltr
```

---

# 🔬 Lab 2 – Remove a File

Suppose a file named:

```text
file1
```

exists in the current directory.

Delete it using:

```bash
rm file1
```

The system may ask for confirmation.

Example:

```text
rm: remove regular file 'file1'?
```

Type:

```text
y
```

Press **Enter**.

The file will be deleted.

---

# 🔬 Lab 3 – Verify the File

Display the directory contents.

```bash
ls
```

The deleted file should no longer appear.

---

# 🔬 Lab 4 – Create a Directory

Create a new directory.

```bash
mkdir test
```

Verify:

```bash
ls
```

---

# 🔬 Lab 5 – Attempt to Remove the Directory

Try removing the directory using:

```bash
rm test
```

Expected Output:

```text
rm: cannot remove 'test': Is a directory
```

By default, the `rm` command does **not** remove directories.

---

# 🔬 Lab 6 – Remove a Directory Recursively

To delete a directory, use the recursive option.

```bash
rm -r test
```

The system may ask for confirmation.

Type:

```text
y
```

The directory will be deleted.

---

# Understanding the `-r` Option

The `-r` option means:

> **Recursive**

It tells Linux to:

- Enter the directory.
- Delete all files.
- Delete all subdirectories.
- Finally remove the directory itself.

---

# 🔬 Lab 7 – Remove an Empty Directory Using `rmdir`

Create another directory.

```bash
mkdir test1
```

Remove it using:

```bash
rmdir test1
```

The directory will be deleted successfully because it is empty.

---

# What is the `rmdir` Command?

The `rmdir` command is specifically designed to remove:

> **Empty directories only**

If the directory contains files, the command will fail.

---

# 🔬 Lab 8 – Create a Non-Empty Directory

Create another directory.

```bash
mkdir test2
```

Move inside it.

```bash
cd test2
```

Create three files.

```bash
touch file1 file2 file3
```

Return to the parent directory.

```bash
cd ..
```

---

# 🔬 Lab 9 – Attempt to Remove the Non-Empty Directory

Try:

```bash
rmdir test2
```

Expected Output:

```text
rmdir: failed to remove 'test2': Directory not empty
```

The `rmdir` command only removes empty directories.

---

# 🔬 Lab 10 – Remove the Non-Empty Directory

Use:

```bash
rm -r test2
```

The system may ask for confirmation for:

- Entering the directory.
- Deleting each file.
- Removing the directory.

Type:

```text
y
```

for each confirmation.

The directory and all its contents will be removed.

---

# 🔬 Lab 11 – Forcefully Remove a Directory

Create another directory.

```bash
mkdir test3
```

Move inside it.

```bash
cd test3
```

Create a few files.

```bash
touch file1 file2 file3
```

Return to the parent directory.

```bash
cd ..
```

Now remove the directory without any confirmation.

```bash
rm -rf test3
```

The directory and all files inside it are deleted immediately.

No confirmation is displayed.

---

# Understanding the `-f` Option

The `-f` option means:

> **Force**

It tells Linux:

- Do not ask for confirmation.
- Ignore prompts.
- Delete immediately.

When combined with `-r`:

```bash
rm -rf directory
```

Linux recursively deletes everything without asking.

---

# ⚠️ Warning: `rm -rf`

The following command is one of the most powerful and dangerous commands in Linux.

```bash
rm -rf directory_name
```

If used incorrectly, it can permanently delete important files and directories.

Always double-check the directory name before pressing **Enter**.

---

# Comparison: `rm` vs `rmdir`

| Command | Purpose |
|----------|---------|
| `rm file` | Remove a file |
| `rm -r directory` | Remove a directory recursively |
| `rm -rf directory` | Forcefully remove a directory and its contents |
| `rmdir directory` | Remove an empty directory only |

---

# Directory Structure Example

Before deletion:

```text
test3
├── file1
├── file2
└── file3
```

After running:

```bash
rm -rf test3
```

The directory and all files are permanently removed.

---

# 🧪 Practice Exercises

---

## Exercise 1

Create three files.

```bash
touch file1 file2 file3
```

Delete one file.

```bash
rm file1
```

---

## Exercise 2

Create an empty directory.

```bash
mkdir backup
```

Remove it.

```bash
rmdir backup
```

---

## Exercise 3

Create a directory with files.

```bash
mkdir data
cd data
touch a b c
cd ..
```

Remove it recursively.

```bash
rm -r data
```

---

## Exercise 4

Create another directory.

```bash
mkdir temp
cd temp
touch x y z
cd ..
```

Delete it forcefully.

```bash
rm -rf temp
```

---

## Exercise 5

Verify the deletion.

```bash
ls
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

You try to remove a directory using:

```bash
rm directory
```

Error:

```text
Is a directory
```

Solution:

```bash
rm -r directory
```

---

### Scenario 2

You try to remove a directory using:

```bash
rmdir directory
```

Error:

```text
Directory not empty
```

Solution:

```bash
rm -r directory
```

---

### Scenario 3

You do not want Linux to ask for confirmation.

Command:

```bash
rm -rf directory
```

Use this command carefully.

---

### Scenario 4

You accidentally deleted the wrong directory using:

```bash
rm -rf
```

Unfortunately, there is no built-in undo command in Linux.

Always verify the path before running `rm -rf`.

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `rm file` | Remove a file |
| `rm -r directory` | Remove a directory recursively |
| `rm -rf directory` | Forcefully remove a directory |
| `rmdir directory` | Remove an empty directory |
| `mkdir test` | Create a directory |
| `touch file` | Create an empty file |
| `ls` | List directory contents |

---

# 📖 Key Takeaways

- `rm` removes files by default.
- `rm -r` removes directories and their contents recursively.
- `rm -rf` removes directories without asking for confirmation.
- `rmdir` only removes empty directories.
- Always verify the directory before using `rm -rf`.
- Use the force option (`-f`) carefully because deleted files cannot be easily recovered.

---

# 💡 Remember

> **Think of `rm` as permanently throwing something into a shredder.**
>
> - `rm` deletes files.
> - `rm -r` deletes directories and everything inside them.
> - `rm -rf` deletes immediately without asking.
> - `rmdir` only removes empty directories.
>
> **Golden Rules:**
>
> ```text
> Remove File            = rm file
>
> Remove Empty Directory = rmdir directory
>
> Remove Directory       = rm -r directory
>
> Force Remove           = rm -rf directory
> ```
>
> **Always be extremely careful when using `rm -rf`, as it permanently deletes data without confirmation.**