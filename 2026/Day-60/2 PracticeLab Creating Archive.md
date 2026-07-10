# MODULE 07 – Practice Lab: Creating a TAR Archive
> **Hands-on Practice Lab – Creating and Viewing TAR Archives**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Create a TAR archive.
- Understand the most common TAR options.
- Archive multiple files into a single file.
- Verify that the archive has been created successfully.
- Move the archive to another location.
- View the contents of a TAR archive without extracting it.

---

# 📖 Introduction

One of the most common tasks performed by a Linux System Administrator is creating backups.

Instead of copying many files individually, Linux provides the **tar** utility, which combines multiple files into a single archive.

This makes backups:

- Easier to manage
- Easier to transfer
- Easier to store

---

# Understanding the TAR Command

The basic command is:

```bash
tar
```

The `tar` command requires options that tell it what action to perform.

---

# Common TAR Options

| Option | Meaning |
|---------|---------|
| `-c` | Create a new archive |
| `-v` | Verbose mode (shows progress) |
| `-f` | Specify the archive file name |
| `-t` | List the contents of an archive |
| `-x` | Extract files from an archive |

---

# Understanding the Create Syntax

General syntax:

```bash
tar -cvf archive_name.tar file1 file2 file3
```

### Explanation

- `-c` → Create a new archive
- `-v` → Display every file being archived
- `-f` → Specify the archive filename

Example:

```bash
tar -cvf backup.tar file1 file2 file3
```

This command creates:

```text
backup.tar
```

which contains all three files.

---

# Lab 1 – Navigate to the Files

Move to a directory containing multiple files.

Example:

```bash
cd /var/log
```

Display the files.

```bash
ls
```

You should see multiple log files.

---

# Lab 2 – Create a TAR Archive

In this lab, we will archive the boot log files.

Run:

```bash
tar -cvf boot.log.tar boot.log*
```

### Explanation

| Part | Meaning |
|------|---------|
| `tar` | Archive utility |
| `-c` | Create archive |
| `-v` | Verbose output |
| `-f` | Archive filename follows |
| `boot.log.tar` | Name of the archive |
| `boot.log*` | Include every file beginning with `boot.log` |

The wildcard (`*`) includes all matching files.

For example:

```text
boot.log
boot.log.1
boot.log.2
boot.log.old
```

All of these files will be included in the archive.

---

# Lab 3 – Verify the Archive

Display the directory contents.

```bash
ls -l
```

You should now see:

```text
boot.log.tar
```

This confirms that the archive has been created successfully.

---

# Lab 4 – Create a Backup Directory

Create a new directory inside `/tmp`.

```bash
mkdir /tmp/backup
```

Verify:

```bash
ls /tmp
```

---

# Lab 5 – Move the Archive

Move the archive into the backup directory.

```bash
mv boot.log.tar /tmp/backup/
```

Verify:

```bash
ls /tmp/backup
```

Expected Output:

```text
boot.log.tar
```

---

# Lab 6 – View Archive Contents

One of the useful features of TAR is that you can view the contents without extracting the archive.

Command:

```bash
tar -tf /tmp/backup/boot.log.tar
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-t` | List archive contents |
| `-f` | Archive filename |

Example Output:

```text
boot.log
boot.log.1
boot.log.2
boot.log.old
```

This confirms which files are stored inside the archive.

---

# Wildcards in TAR

Instead of typing every filename individually:

```bash
tar -cvf backup.tar file1 file2 file3 file4
```

You can use a wildcard.

Example:

```bash
tar -cvf backup.tar file*
```

This archives every file beginning with:

```text
file
```

Examples:

```text
file1
file2
file3
fileA
file_backup
```

---

# Practice Exercises

---

## Exercise 1

Navigate to the log directory.

```bash
cd /var/log
```

---

## Exercise 2

List all files.

```bash
ls
```

---

## Exercise 3

Create a TAR archive.

```bash
tar -cvf boot.log.tar boot.log*
```

---

## Exercise 4

Verify the archive exists.

```bash
ls -l
```

---

## Exercise 5

Create a backup directory.

```bash
mkdir /tmp/backup
```

---

## Exercise 6

Move the archive.

```bash
mv boot.log.tar /tmp/backup/
```

---

## Exercise 7

Verify the backup directory.

```bash
ls /tmp/backup
```

---

## Exercise 8

View the archive contents.

```bash
tar -tf /tmp/backup/boot.log.tar
```

---

# Troubleshooting Scenarios

### Scenario 1

The archive file was created, but you are unsure which files were included.

Command:

```bash
tar -tf boot.log.tar
```

---

### Scenario 2

You accidentally archived the wrong files.

Solution:

Delete the archive and recreate it.

```bash
rm boot.log.tar
```

Then:

```bash
tar -cvf boot.log.tar boot.log*
```

---

### Scenario 3

You want to archive many files without typing every filename.

Solution:

Use a wildcard.

```bash
tar -cvf backup.tar *
```

or

```bash
tar -cvf backup.tar file*
```

---

# Quick Revision

| Command | Purpose |
|----------|---------|
| `tar -cvf backup.tar files` | Create a TAR archive |
| `tar -tf backup.tar` | List archive contents |
| `mkdir /tmp/backup` | Create backup directory |
| `mv backup.tar /tmp/backup` | Move archive |
| `ls -l` | Verify archive exists |

---

# 📌 Key Takeaways

- `tar -c` creates a new archive.
- `-v` displays verbose output while archiving.
- `-f` specifies the archive filename.
- Wildcards (`*`) can archive multiple files easily.
- `tar -tf` lists archive contents without extracting files.
- TAR archives simplify backups and file transfers.

---

# 💡 Remember

> **Think of a TAR archive as a storage box.**
>
> Instead of carrying many separate files, you place everything inside one box (**archive**).
>
> Before restoring anything, you can always look inside the box using:
>
> ```bash
> tar -tf archive.tar
> ```
>
> This allows you to verify its contents without extracting any files.
