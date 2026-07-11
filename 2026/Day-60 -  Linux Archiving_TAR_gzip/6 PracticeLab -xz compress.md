# MODULE 07 – Practice Lab: Creating a Compressed TAR Archive Using xz
> **Hands-on Practice Lab – Compressing TAR Archives Using xz**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Create a compressed TAR archive using **xz**.
- Understand the `-J` option of the `tar` command.
- Compare the sizes of:
  - Normal TAR archive
  - gzip compressed archive
  - bzip2 compressed archive
  - xz compressed archive
- Learn why **xz** provides the best compression ratio.
- Learn how to extract an xz compressed archive.

---

# 📖 Introduction

In the previous practice labs, we learned how to compress TAR archives using:

- gzip
- bzip2

In this lab, we will learn another compression method called **xz**.

Among the commonly used Linux compression methods, **xz** provides the **best compression ratio**.

This means:

- Smaller archive size
- Better storage efficiency
- Less bandwidth usage

However,

- Compression takes longer.
- Extraction is also slightly slower.

---

# What is xz?

**xz** is a modern compression utility used in Linux.

Characteristics:

- Best compression ratio
- Smallest archive size
- Slower than gzip
- Slower than bzip2
- Excellent for long-term backups

---

# xz Option

When using the `tar` command, the option for **xz** compression is:

```bash
-J
```

> **Important:**  
> The correct option is **uppercase `-J`**, not lowercase `-j`.

---

# TAR + xz Syntax

General syntax:

```bash
tar -cvJf archive.tar.xz directory
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-c` | Create a new archive |
| `-v` | Display verbose output |
| `-J` | Use xz compression |
| `-f` | Specify the archive filename |

---

# 🔬 Lab 1 – Create an xz Compressed Archive

In this lab, we will create an xz compressed backup of the `/etc` directory.

Command:

```bash
tar -cvJf /root/etcbackup.tar.xz /etc
```

### Explanation

| Part | Meaning |
|------|---------|
| `tar` | Archive utility |
| `-c` | Create archive |
| `-v` | Verbose output |
| `-J` | Use xz compression |
| `-f` | Archive filename |
| `/root/etcbackup.tar.xz` | Archive name and location |
| `/etc` | Directory being archived |

This command creates an **xz compressed archive** of the `/etc` directory.

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
etcbackup.tar.xz
```

You should now have four archive files:

- TAR Archive
- gzip Archive
- bzip2 Archive
- xz Archive

---

# 🔬 Lab 3 – Compare Archive Sizes

Compare the sizes of all archive files.

```bash
ls -lh /root/etcbackup.tar*
```

Example Output:

```text
26M   etcbackup.tar
6M    etcbackup.tar.gz
5M    etcbackup.tar.bz2
4M    etcbackup.tar.xz
```

Notice:

- TAR archive is the largest.
- gzip compresses significantly.
- bzip2 compresses even more.
- xz provides the smallest archive.

---

# Understanding the Results

Suppose the archive sizes are:

| Archive | Size |
|----------|------|
| TAR | 26 MB |
| gzip | 6 MB |
| bzip2 | 5 MB |
| xz | 4 MB |

This demonstrates that **xz provides the highest compression ratio** among the commonly used compression methods.

---

# Why Use xz?

Use **xz** when:

- You want the smallest possible backup.
- Disk space is limited.
- Long-term storage is important.
- Compression speed is less important than file size.

---

# Practical Scenario

Suppose you have a backup that is:

```text
26 MB
```

Using:

- gzip → 6 MB
- bzip2 → 5 MB
- xz → 4 MB

When transferring large backups over a slow network, **xz** saves the most bandwidth and storage.

---

# 🔬 Lab 4 – View TAR Manual

To view all available TAR options and supported compression methods:

```bash
man tar
```

You can search inside the manual by typing:

```text
/J
```

or

```text
compression
```

This displays all supported compression options, including:

- gzip (`-z`)
- bzip2 (`-j`)
- xz (`-J`)

---

# 🔬 Lab 5 – Extract an xz Compressed Archive

To extract an xz compressed archive:

```bash
tar -xvJf /root/etcbackup.tar.xz
```

### Explanation

| Option | Meaning |
|---------|---------|
| `-x` | Extract files |
| `-v` | Verbose output |
| `-J` | Use xz decompression |
| `-f` | Archive filename |

After extraction, the original directory and files will be restored.

---

# 🧪 Practice Exercises

---

## Exercise 1

Create an xz compressed archive.

```bash
tar -cvJf /root/etcbackup.tar.xz /etc
```

---

## Exercise 2

Verify the archive.

```bash
ls -ltr /root
```

---

## Exercise 3

Compare all archive sizes.

```bash
ls -lh /root/etcbackup.tar*
```

---

## Exercise 4

View the TAR manual.

```bash
man tar
```

Search for:

```text
compression
```

---

## Exercise 5

Extract the xz archive.

```bash
tar -xvJf /root/etcbackup.tar.xz
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

The archive was created but is not compressed.

Check that you used the **uppercase `-J`** option.

Correct command:

```bash
tar -cvJf backup.tar.xz /etc
```

---

### Scenario 2

The archive extension is `.tar`.

This means the archive is **not compressed**.

For xz compression, the extension should be:

```text
.tar.xz
```

---

### Scenario 3

You need the smallest possible archive.

Use **xz**.

Command:

```bash
tar -cvJf backup.tar.xz /etc
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `tar -cvJf backup.tar.xz /etc` | Create an xz compressed archive |
| `tar -xvJf backup.tar.xz` | Extract an xz archive |
| `ls -ltr /root` | Verify archive creation |
| `ls -lh /root/etcbackup.tar*` | Compare archive sizes |
| `man tar` | View TAR documentation |

---

# 📖 Key Takeaways

- `-J` enables **xz compression** in the TAR command.
- `.tar.xz` archives are usually the **smallest** among common compression formats.
- xz offers the **best compression ratio**.
- xz compression takes longer than gzip and bzip2.
- Use xz when storage efficiency is more important than compression speed.

---

# 💡 Remember

> **Think of xz as the strongest vacuum compressor.**
>
> - **TAR** puts everything into one suitcase.
> - **gzip** compresses the suitcase.
> - **bzip2** compresses it even more.
> - **xz** compresses it the most, producing the smallest archive, although it takes the longest time.
>
> For long-term backups and maximum storage savings, **xz is usually the best choice**.