# Linux Practice Lab – Understanding tar and Compression
# DAY-59
> JULY 05, 2026
## Objective

In this lab you will learn how to:

- Create tar archives
- Compress archives using different compression algorithms
- Compare archive sizes
- Understand the advantages and disadvantages of each compression method
- Inspect archive contents
- Extract archives

---

# Lab Environment

- Operating System: Rocky Linux 9
- User: root (or sudo user)
- Working Directory: `/mnt/backup`

---

# Scenario

Your Linux server contains important configuration files inside the `/etc` directory.

As a Linux System Administrator, your task is to create several backups using different compression methods and compare which one saves the most disk space.

---

# Task 1 – Create a Backup Directory

Create a directory for storing backups.

```bash
mkdir -p /mnt/backup
```

Move into the backup directory.

```bash
cd /mnt/backup
```

Verify your location.

```bash
pwd
```

Expected Output

```
/mnt/backup
```

---

# Task 2 – Create an Uncompressed tar Archive

Create a standard tar archive.

```bash
tar -cvf /mnt/backup/etcbackup.tar /etc
```

Explanation

| Option | Meaning |
|---------|---------|
| -c | Create archive |
| -v | Verbose output |
| -f | Specify archive filename |

Notice the message:

```
tar: Removing leading '/' from member names
```

This is completely normal.

Linux removes the leading `/` so that extracting the archive cannot accidentally overwrite the entire system.

---

# Task 3 – Create a gzip Compressed Archive

```bash
tar -cvzf /mnt/backup/etcbackup.tar.gz /etc
```

Explanation

| Option | Meaning |
|---------|---------|
| -z | Compress using gzip |

---

# Task 4 – Create a bzip2 Compressed Archive

```bash
tar -cvjf /mnt/backup/etcbackup.tar.bz2 /etc
```

Explanation

| Option | Meaning |
|---------|---------|
| -j | Compress using bzip2 |

---

# Task 5 – Create an xz Compressed Archive

```bash
tar -cvJf /mnt/backup/etcbackup.tar.xz /etc
```

Explanation

| Option | Meaning |
|---------|---------|
| -J | Compress using xz |

---

# Task 6 – View the Backup Files

Display all created files.

```bash
ls -lh
```

Example

```
-rw-r--r--  etcbackup.tar
-rw-r--r--  etcbackup.tar.gz
-rw-r--r--  etcbackup.tar.bz2
-rw-r--r--  etcbackup.tar.xz
```

Question

Which file appears to be the largest?

Answer:

```
etcbackup.tar
```

---

# Task 7 – Compare Actual Disk Usage

Run:

```bash
du -sk *
```

Example Output

```
22000   etcbackup.tar
4744    etcbackup.tar.gz
3872    etcbackup.tar.bz2
1080    etcbackup.tar.xz
```

---

# Observation

| Archive | Approximate Size |
|-----------|----------------:|
| etcbackup.tar | 22 MB |
| etcbackup.tar.gz | 4.7 MB |
| etcbackup.tar.bz2 | 3.8 MB |
| etcbackup.tar.xz | 1.1 MB |

---

# Discussion

Which compression algorithm produced the smallest archive?

Answer:

```
xz
```

Which archive was created the fastest?

Answer:

```
tar (no compression)
```

Which compression method is most commonly used on Linux?

Answer:

```
gzip (.tar.gz)
```

---

# Understanding the Compression Algorithms

| Compression | Extension | Speed | Compression Ratio |
|-------------|-----------|-------|-------------------|
| None | .tar | Fastest | None |
| gzip | .tar.gz | Fast | Good |
| bzip2 | .tar.bz2 | Slower | Better |
| xz | .tar.xz | Slowest | Best |

---

# Task 8 – List Archive Contents Without Extracting

View the contents of the tar archive.

```bash
tar -tvf etcbackup.tar
```

View the gzip archive.

```bash
tar -tvzf etcbackup.tar.gz
```

View the bzip2 archive.

```bash
tar -tvjf etcbackup.tar.bz2
```

View the xz archive.

```bash
tar -tvJf etcbackup.tar.xz
```

Question

Did any files get extracted?

Answer:

```
No
```

---

# Task 9 – Extract the Archives

Create extraction directories.

```bash
mkdir extract-tar
mkdir extract-gzip
mkdir extract-bzip2
mkdir extract-xz
```

Extract each archive.

Standard tar

```bash
tar -xvf etcbackup.tar -C extract-tar
```

gzip

```bash
tar -xvzf etcbackup.tar.gz -C extract-gzip
```

bzip2

```bash
tar -xvjf etcbackup.tar.bz2 -C extract-bzip2
```

xz

```bash
tar -xvJf etcbackup.tar.xz -C extract-xz
```

Verify.

```bash
ls extract-tar
```

---

# Task 10 – Verify Extracted Files

Compare the extracted directory with the original.

```bash
ls /etc
```

```bash
ls extract-tar/etc
```

Question

Do both contain the same files?

Answer:

```
Yes
```

---

# Common tar Options

| Option | Description |
|---------|-------------|
| -c | Create archive |
| -x | Extract archive |
| -t | List archive contents |
| -v | Verbose mode |
| -f | Archive filename |
| -z | gzip compression |
| -j | bzip2 compression |
| -J | xz compression |
| -C | Change extraction directory |

---

# RHCSA Challenge 1

Without looking at your notes:

Create a gzip backup of `/var/log` named:

```
logs.tar.gz
```

Store it inside:

```
/mnt/backup
```

---

# RHCSA Challenge 2

List the contents without extracting it.

---

# RHCSA Challenge 3

Extract the archive into:

```
/tmp/logrestore
```

---

# RHCSA Challenge 4

Which command shows the amount of disk space consumed by every archive?

---

# RHCSA Challenge 5

Which command shows the human-readable file size?

---

# RHCSA Challenge 6

Which archive type generally provides the best compression?

---

# Knowledge Check

1. What does the `-c` option do?

2. What does `-x` do?

3. What does `-t` do?

4. What does `-v` do?

5. What does `-f` do?

6. Which option enables gzip compression?

7. Which option enables bzip2 compression?

8. Which option enables xz compression?

9. Which compression produced the smallest file in this lab?

10. Why does tar display:

```
Removing leading '/' from member names
```

---

# Summary

After completing this lab you should be able to:

✅ Create tar archives

✅ Compress archives with gzip

✅ Compress archives with bzip2

✅ Compress archives with xz

✅ Compare archive sizes

✅ List archive contents

✅ Extract archives

✅ Understand when to use each compression algorithm

These are essential Linux System Administration skills and are commonly tested in RHCSA and used daily in production Linux environments.