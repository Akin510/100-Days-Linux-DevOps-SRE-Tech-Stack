# MODULE 07 – Practice Lab: Extracting TAR Archives
> **Hands-on Practice Lab – Extracting TAR Archives and Backing Up Directories**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- View the contents of a TAR archive.
- Extract files from a TAR archive.
- Restore files from an archive.
- Create a backup of an entire directory.
- Verify the archive size.
- Understand how TAR archives simplify backups and file transfers.

---

# 📖 Introduction

In the previous lab, we learned how to create a TAR archive.

Now imagine you have transferred that archive from one Linux server to another.

The next step is to restore (extract) the files from the archive.

Linux uses the **tar** command to extract archived files.

---

# Review Previous Lab

Previously, we created an archive named:

```text
boot.log.tar
```

This archive contains multiple boot log files.

Instead of transferring many individual files, we only need to transfer this single archive.

Once the archive reaches the destination server, it can easily be extracted.

---

# Lab 1 – View Archive Contents

Before extracting an archive, it is a good practice to check what files it contains.

Command:

```bash
tar -tf boot.log.tar
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-t` | List archive contents |
| `-f` | Specify archive filename |

Example Output:

```text
boot.log
boot.log.1
boot.log.2
boot.log.old
```

This confirms which files are stored inside the archive.

---

# Lab 2 – Extract a TAR Archive

To extract all files from an archive, use:

```bash
tar -xvf boot.log.tar
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-x` | Extract files |
| `-v` | Display verbose output |
| `-f` | Specify archive filename |

As the command runs, each extracted file is displayed on the screen.

After extraction, all archived files will appear in the current directory.

---

# Lab 3 – Verify Extracted Files

List the directory contents.

```bash
ls
```

You should now see:

```text
boot.log
boot.log.1
boot.log.2
boot.log.old
```

This confirms that the archive has been extracted successfully.

---

# Practical Scenario

Suppose you have:

- Linux Server A
- Linux Server B

### Step 1

Create an archive on Server A.

```text
boot.log.tar
```

### Step 2

Transfer the archive to Server B.

### Step 3

On Server B, extract the archive.

```bash
tar -xvf boot.log.tar
```

All original files are restored.

---

# Lab 4 – Backup an Entire Directory

The TAR command can also archive entire directories.

One of the most important directories in Linux is:

```text
/etc
```

This directory contains:

- System configuration files
- Service configuration
- Network configuration
- User configuration
- Security configuration

Creating a backup of `/etc` is a common administrative task.

---

# Lab 5 – Create an Archive of `/etc`

Run:

```bash
tar -cvf /root/etcbackup.tar /etc
```

### Explanation

| Part | Meaning |
|------|---------|
| `tar` | Archive utility |
| `-c` | Create archive |
| `-v` | Verbose output |
| `-f` | Archive filename |
| `/root/etcbackup.tar` | Archive location and name |
| `/etc` | Directory being archived |

The command creates:

```text
/root/etcbackup.tar
```

which contains the complete `/etc` directory.

---

# Lab 6 – Verify the Archive

Display the archive.

```bash
ls -lh /root/etcbackup.tar
```

Example Output:

```text
-rw-r--r--  root root 26M etcbackup.tar
```

The `-h` option displays the size in a human-readable format (KB, MB, GB).

---

# Lab 7 – Check Archive Size

Another way to check the archive size:

```bash
du -sh /root/etcbackup.tar
```

Example Output:

```text
26M
```

This indicates that the archive occupies approximately **26 MB**.

---

# Practice Exercises

---

## Exercise 1

View the contents of the archive.

```bash
tar -tf boot.log.tar
```

---

## Exercise 2

Extract the archive.

```bash
tar -xvf boot.log.tar
```

---

## Exercise 3

Verify the extracted files.

```bash
ls
```

---

## Exercise 4

Create a backup of the `/etc` directory.

```bash
tar -cvf /root/etcbackup.tar /etc
```

---

## Exercise 5

Verify the archive.

```bash
ls -lh /root/etcbackup.tar
```

---

## Exercise 6

Check the archive size.

```bash
du -sh /root/etcbackup.tar
```

---

# Troubleshooting Scenarios

### Scenario 1

You forgot which files are inside an archive.

Command:

```bash
tar -tf archive.tar
```

---

### Scenario 2

You received an archive from another server and need to restore the files.

Command:

```bash
tar -xvf archive.tar
```

---

### Scenario 3

You need to back up the `/etc` directory before making configuration changes.

Command:

```bash
tar -cvf /root/etcbackup.tar /etc
```

---

### Scenario 4

You want to verify the size of the backup archive.

Command:

```bash
du -sh /root/etcbackup.tar
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `tar -tf archive.tar` | View archive contents |
| `tar -xvf archive.tar` | Extract archive |
| `tar -cvf /root/etcbackup.tar /etc` | Create `/etc` backup |
| `ls -lh archive.tar` | View archive size |
| `du -sh archive.tar` | Display archive size |

---

# 📖 Key Takeaways

- `tar -tf` displays the contents of an archive without extracting it.
- `tar -xvf` extracts all files from an archive.
- TAR archives are ideal for transferring files between Linux servers.
- Entire directories such as `/etc` can be archived using TAR.
- Always verify your archive after creating it.
- Use `ls -lh` or `du -sh` to check the archive size.

---

# 💡 Remember

> **Think of a TAR archive as a sealed storage box.**
>
> Before opening the box, you can look at its contents using:
>
> ```bash
> tar -tf archive.tar
> ```
>
> When you're ready to restore everything inside, simply extract it with:
>
> ```bash
> tar -xvf archive.tar
> ```
>
> This makes TAR one of the most useful backup and migration tools for Linux administrators.