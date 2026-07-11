# MODULE 07 – Practice Lab: Creating a Compressed TAR Archive Using bzip2
> **Hands-on Practice Lab – Compressing TAR Archives Using bzip2**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Create a compressed TAR archive using **bzip2**.
- Understand the `-j` option of the `tar` command.
- Compare the sizes of:
  - Normal TAR archive
  - gzip compressed archive
  - bzip2 compressed archive
- Understand when to use **bzip2** compression.

---

# 📖 Introduction

In the previous practice lab, we learned how to compress a TAR archive using the **gzip** compression method.

In this lab, we will use another compression method called **bzip2**.

Compared to **gzip**, **bzip2** generally produces **smaller archive files**, although it is usually **slower** and is **not as widely available** as gzip.

---

# What is bzip2?

**bzip2** is a compression utility used to reduce the size of files.

Characteristics:

- Better compression than **gzip**
- Smaller archive size
- Slower compression speed
- Less commonly used than gzip

---

# bzip2 Option

When using the **tar** command, the option for **bzip2** compression is:

```bash
-j
```

This tells `tar` to compress the archive using the **bzip2** algorithm.

---

# TAR + bzip2 Syntax

General syntax:

```bash
tar -cvjf archive.tar.bz2 directory
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-c` | Create a new archive |
| `-v` | Display verbose output |
| `-j` | Use bzip2 compression |
| `-f` | Specify the archive filename |

---

# 🔬 Lab 1 – Create a bzip2 Compressed Archive

In this lab, we will create a compressed backup of the `/etc` directory.

Command:

```bash
tar -cvjf /root/etcbackup.tar.bz2 /etc
```

### Explanation

| Part | Meaning |
|------|---------|
| `tar` | Archive utility |
| `-c` | Create archive |
| `-v` | Verbose output |
| `-j` | Use bzip2 compression |
| `-f` | Archive filename |
| `/root/etcbackup.tar.bz2` | Archive name and location |
| `/etc` | Directory being archived |

This command creates a **bzip2 compressed archive** of the `/etc` directory.

---

# 🔬 Lab 2 – Verify the Archive

Display the contents of the `/root` directory.

```bash
ls -ltr /root
```

Expected Output:

```text
etcbackup.tar
etcbackup.tar.gz
etcbackup.tar.bz2
```

You should now have:

- Normal TAR archive
- gzip compressed archive
- bzip2 compressed archive

---

# 🔬 Lab 3 – Compare Archive Sizes

Compare the sizes of all three archive files.

```bash
ls -lh /root/etcbackup.tar*
```

Example Output:

```text
26M   etcbackup.tar
6M    etcbackup.tar.gz
5M    etcbackup.tar.bz2
```

Notice that:

- The normal TAR archive is the largest.
- The gzip archive is much smaller.
- The bzip2 archive is even smaller than the gzip archive.

---

# Understanding the Results

Suppose the archive sizes are:

| Archive | Size |
|----------|------|
| TAR | 26 MB |
| gzip | 6 MB |
| bzip2 | 5 MB |

This shows that **bzip2 provides better compression** than **gzip**, although it generally takes longer to compress files.

---

# Why Use bzip2?

Use **bzip2** when:

- Disk space is limited.
- You want a smaller backup.
- Compression ratio is more important than compression speed.

---

# Practical Scenario

Suppose you need to send a large backup over a slow network.

Instead of sending:

```text
26 MB
```

You can compress it to:

```text
5 MB
```

This reduces:

- Upload time
- Download time
- Bandwidth usage
- Storage requirements

---

# 🧪 Practice Exercises

---

## Exercise 1

Create a bzip2 compressed archive.

```bash
tar -cvjf /root/etcbackup.tar.bz2 /etc
```

---

## Exercise 2

Verify the archive.

```bash
ls -ltr /root
```

---

## Exercise 3

Compare the archive sizes.

```bash
ls -lh /root/etcbackup.tar*
```

---

## Exercise 4

Check the archive size using `du`.

```bash
du -sh /root/etcbackup.tar.bz2
```

---

## Exercise 5

Compare:

- TAR
- gzip
- bzip2

Determine which one provides the smallest archive.

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

The archive is not compressed.

Check that you used the `-j` option.

Correct command:

```bash
tar -cvjf backup.tar.bz2 /etc
```

---

### Scenario 2

The archive extension is `.tar`.

This means it is only archived and **not compressed**.

For bzip2, the correct extension is:

```text
.tar.bz2
```

---

### Scenario 3

You want better compression than gzip.

Use **bzip2**.

Command:

```bash
tar -cvjf backup.tar.bz2 /etc
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `tar -cvjf backup.tar.bz2 /etc` | Create a bzip2 compressed archive |
| `ls -ltr /root` | Verify archive creation |
| `ls -lh /root/etcbackup.tar*` | Compare archive sizes |
| `du -sh backup.tar.bz2` | Check archive size |

---

# 📖 Key Takeaways

- `-j` enables **bzip2** compression in the `tar` command.
- `.tar.bz2` archives are usually **smaller than `.tar.gz`** archives.
- **bzip2** provides better compression but is slower than **gzip**.
- It is useful when minimizing file size is more important than compression speed.
- Comparing archive sizes helps you choose the most suitable compression method.

---

# 💡 Remember

> **Think of compression methods like packing luggage for travel.**
>
> - A **TAR archive** is like putting everything into one suitcase.
> - **gzip** squeezes the suitcase to make it smaller.
> - **bzip2** squeezes it even more, making it smaller than gzip, but it takes longer to pack.
>
> The smaller the archive, the easier and faster it is to store and transfer.
```