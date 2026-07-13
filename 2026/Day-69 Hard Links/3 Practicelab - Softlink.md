# MODULE 10 – Practice Lab: Symbolic (Soft) Links
> **Hands-on Practice Lab – Creating and Understanding Symbolic (Soft) Links in Linux**

---

# 🎯 Lab Objectives

By the end of this lab, you will be able to:

- Create a Symbolic (Soft) Link using the `ln -s` command.
- Verify a Soft Link using inode numbers.
- Identify a Symbolic Link in a directory listing.
- Understand what happens when the original file is deleted.
- Create Soft Links in the same directory.
- Create Soft Links in different directories.
- Understand the importance of using absolute paths.
- Create Soft Links across different file systems.
- Compare Soft Links with Hard Links.

---

# 📖 Introduction

In the previous lab, we learned how to create **Hard Links**.

In this lab, we will create **Symbolic Links (Soft Links)** and understand how they behave.

Unlike a Hard Link, a Soft Link does **not** point directly to the file's inode.

Instead, it stores the **path** to the original file.

Think of a Soft Link as a shortcut that tells Linux where the original file is located.

---

# 🔬 Lab Scenario

Suppose we have the following original file:

```text
original-file
```

Instead of creating another filename that shares the same inode, we want to create a Symbolic Link that simply points to the original file.

---

# Lab Environment

Move to your working directory.

Example:

```bash
cd /root/links
```

Verify your current location:

```bash
pwd
```

Example Output:

```text
/root/links
```

Initially, the directory is empty.

---

# 🔬 Lab 1 – Create the Original File

Create the original file:

```bash
echo "This is my soft link test file." > original-file
```

Display the file:

```bash
cat original-file
```

Expected Output:

```text
This is my soft link test file.
```

---

# Verify the Original File

Display detailed information:

```bash
ls -li
```

Example Output:

```text
1234567 original-file
```

Notice the inode number.

Every file in Linux has a unique inode number.

---

# 🔬 Lab 2 – Create a Symbolic Link

The command used to create a Soft Link is:

```bash
ln -s
```

General Syntax:

```bash
ln -s <target_file> <link_name>
```

Example:

```bash
ln -s original-file softlink1
```

Where:

- `original-file` → Original file
- `softlink1` → Symbolic Link

---

# Verify the Soft Link

Run:

```bash
ls -li
```

Example Output:

```text
1234567 original-file

7654321 softlink1 -> original-file
```

Notice two important things:

1. The Soft Link has a **different inode number**.
2. The first character is:

```text
l
```

which indicates that it is a Symbolic Link.

---

# Understanding the Directory Listing

Example:

```text
lrwxrwxrwx
```

The first character:

```text
l
```

stands for:

```text
Link
```

This tells Linux that the file is a Symbolic Link.

---

# Soft Link Illustration

```text
Original File
      │
      ▼
original-file
      ▲
      │
softlink1
```

The Soft Link simply points to the original file.

---

# 🔬 Lab 3 – Read the Soft Link

Display the Soft Link:

```bash
cat softlink1
```

Expected Output:

```text
This is my soft link test file.
```

Although you are reading the Soft Link, Linux automatically follows the link and reads the original file.

---

# 🔬 Lab 4 – Delete the Original File

Delete the original file:

```bash
rm original-file
```

Now attempt to read the Soft Link:

```bash
cat softlink1
```

Expected Output:

```text
No such file or directory
```

The Soft Link still exists, but it points to a file that no longer exists.

Therefore, it becomes a **Broken Link**.

---

# Broken Link Illustration

```text
Original File
      ❌ Deleted

Soft Link
      │
      ▼
Broken Link
```

---

# Directory Listing After Deletion

Display the directory:

```bash
ls -l
```

Many Linux distributions display broken links in **red** to indicate that the target file is missing.

This is a visual indication that the Symbolic Link is no longer valid.

---

# Remove the Broken Link

Delete the Soft Link:

```bash
rm softlink1
```

The Broken Link is removed.

---

# 🔬 Lab 5 – Create a New Original File

Create another file:

```bash
echo "This is my second original file." > original-file2
```

Display it:

```bash
cat original-file2
```

---

# 🔬 Lab 6 – Create a Soft Link in Another Directory

Suppose you want to create the Symbolic Link inside:

```text
/tmp
```

The command is:

```bash
ln -s /root/links/original-file2 /tmp/softlink2
```

Notice that the **absolute path** of the original file is used.

---

# Verify the Link

Display:

```bash
ls -l /tmp
```

You should see:

```text
softlink2 -> /root/links/original-file2
```

Now read the file:

```bash
cat /tmp/softlink2
```

Expected Output:

```text
This is my second original file.
```

The Symbolic Link works correctly.

---

# Why Use the Absolute Path?

When creating Symbolic Links in another directory, using the absolute path ensures that Linux can always locate the original file.

Example:

```bash
ln -s /root/links/original-file2 /tmp/softlink2
```

This points directly to the correct file regardless of the current working directory.

---

# 🔬 Lab 7 – Incorrect Relative Path

Suppose you create the link like this:

```bash
ln -s original-file2 /tmp/softlink3
```

This uses a **relative path**.

Now attempt to read it:

```bash
cat /tmp/softlink3
```

Expected Output:

```text
No such file or directory
```

Why?

Because the Symbolic Link looks for:

```text
/tmp/original-file2
```

instead of:

```text
/root/links/original-file2
```

The relative path points to the wrong location.

---

# Relative vs Absolute Path

### Relative Path

```text
original-file2
```

Depends on the current location.

---

### Absolute Path

```text
/root/links/original-file2
```

Always points to the correct file.

Whenever you create Symbolic Links in another directory, **always prefer the absolute path**.

---

# 🔬 Lab 8 – Create a Soft Link Across File Systems

Unlike Hard Links, Symbolic Links can cross file system boundaries.

Suppose:

```text
/boot
```

is a separate file system.

Create the Symbolic Link:

```bash
ln -s /root/links/original-file2 /boot/softlink4
```

Verify:

```bash
cat /boot/softlink4
```

Expected Output:

```text
This is my second original file.
```

The Symbolic Link works successfully even though `/boot` is a different file system.

---

# Why Does This Work?

A Symbolic Link stores only the **path** to the original file.

It does **not** reference the inode directly.

Therefore, Linux can follow the path across different mounted file systems.

---

# Soft Link vs Hard Link

| Feature | Soft Link | Hard Link |
|----------|-----------|-----------|
| Points to | File Path | Inode |
| Same inode | No | Yes |
| Same permissions | No | Yes |
| Broken if original file is deleted | Yes | No |
| Crosses file systems | Yes | No |
| Can link directories | Yes | No |

---

# Practice Exercises

## Exercise 1

Create a practice directory:

```bash
mkdir ~/softlink-lab
```

---

## Exercise 2

Create a file:

```bash
echo "Linux Soft Link Lab" > original.txt
```

---

## Exercise 3

Create a Symbolic Link:

```bash
ln -s original.txt shortcut.txt
```

---

## Exercise 4

Verify the inode numbers:

```bash
ls -li
```

Notice that the inode numbers are different.

---

## Exercise 5

Read the Symbolic Link:

```bash
cat shortcut.txt
```

---

## Exercise 6

Delete the original file:

```bash
rm original.txt
```

Attempt to read the Symbolic Link:

```bash
cat shortcut.txt
```

Observe the error.

---

## Exercise 7

Create a Symbolic Link using an absolute path:

```bash
ln -s /home/student/file.txt /tmp/file-link
```

Verify it.

---

## Exercise 8

Attempt the same operation using a relative path and observe the difference.

---

# 🔧 Troubleshooting

### Problem

The Symbolic Link shows:

```text
No such file or directory
```

Check:

- Does the original file still exist?
- Is the path correct?

---

### Problem

The Symbolic Link is displayed in red.

This usually indicates a **Broken Link**.

The target file is missing.

---

### Problem

The Symbolic Link points to the wrong file.

Verify:

```bash
ls -l
```

Ensure that the target path is correct.

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `ln -s source destination` | Create a Symbolic Link |
| `ls -li` | Display inode numbers |
| `ls -l` | Display link targets |
| `cat file` | Display file contents |
| `rm file` | Delete a file or link |

---

# 📖 Key Takeaways

- A Symbolic Link stores the path to another file.
- A Soft Link has its own inode number.
- The first character `l` identifies a Symbolic Link.
- Deleting the original file breaks the Symbolic Link.
- Soft Links can point to directories.
- Soft Links can cross different file systems.
- Absolute paths are recommended when creating Symbolic Links in other directories.

---

# 💡 Remember

> **Think of a Symbolic Link as a GPS address.**
>
> - The GPS knows where the destination is.
> - If the destination building is demolished, the GPS address still exists—but it no longer leads anywhere useful.
> - Likewise, a Soft Link stores only the path to the original file.
> - If the original file is removed, the Symbolic Link becomes a Broken Link.
>
> **Golden Rule:**
>
> ```text
> Soft Link
>      │
>      ▼
> Points to File Path
>
> Delete Original File
>      │
>      ▼
> Broken Link
> ```
>
> Unlike Hard Links, Symbolic Links are flexible—they can cross file systems and even point to directories.