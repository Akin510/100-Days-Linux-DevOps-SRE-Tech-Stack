# MODULE 07 – Practice Lab: Creating a Compressed TAR Archive
> **Hands-on Practice Lab – Compressing TAR Archives Using gzip**

---

# 🎯 Lab Ka Maqsad

Is practice lab mein aap seekhenge:

- TAR archive ko compress kaise karna hai.
- `gzip` compression method use karna.
- Compressed archive aur normal TAR archive ka size compare karna.
- Compression ke benefits samajhna.
- Backup aur network transfer ke liye compressed archives banana.

---

# 📖 Introduction

Pichlay practice lab mein hum ne seekha tha ke **tar** utility ki madad se multiple files aur directories ka archive kaise create kiya jata hai.

Ab is lab mein hum seekhenge ke us archive ko aur chhota (compress) kaise kiya jaye.

Compression ka sab se bara faida ye hai ke:

- File ka size bohot kam ho jata hai.
- Backup kam storage leta hai.
- Network par file jaldi transfer hoti hai.
- Bandwidth bachti hai.

---

# Compression Methods

Linux multiple compression methods support karta hai.

| Method | Extension | Speed | Compression |
|---------|-----------|--------|-------------|
| `gzip` | `.tar.gz` | Sab se Fast | Good |
| `bzip2` | `.tar.bz2` | Medium | Better |
| `xz` | `.tar.xz` | Slow | Best |
| `compress` | `.tar.Z` | Fast | Legacy |

Is lab mein hum **gzip** compression use karenge.

---

# gzip Option

TAR command mein gzip compression ke liye option hai:

```bash
-z
```

Ye option archive ko automatically compress kar deta hai.

---

# TAR + gzip Syntax

General syntax:

```bash
tar -cvzf archive.tar.gz directory
```

### Explanation

| Option | Matlab |
|---------|---------|
| `-c` | Archive create karo |
| `-v` | Verbose output show karo |
| `-z` | gzip compression use karo |
| `-f` | Archive filename specify karo |

---

# 🔬 Lab 1 – Create a Compressed Archive

Is lab mein hum `/etc` directory ka compressed archive banayenge.

Command:

```bash
tar -cvzf /root/etcbackup.tar.gz /etc
```

### Explanation

| Part | Matlab |
|------|---------|
| `tar` | Archive utility |
| `-c` | Archive create karo |
| `-v` | Verbose mode |
| `-z` | gzip compression |
| `-f` | Archive filename |
| `/root/etcbackup.tar.gz` | Archive ka naam aur location |
| `/etc` | Jis directory ka backup lena hai |

Ye command `/etc` directory ka compressed archive create karegi.

---

# 🔬 Lab 2 – Verify the Archive

Archive ko verify karne ke liye:

```bash
ls -ltr /root
```

Expected Output:

```text
etcbackup.tar
etcbackup.tar.gz
```

Ab aap ke paas do files hongi:

- Normal TAR Archive
- Compressed TAR Archive

---

# 🔬 Lab 3 – Compare File Sizes

Archive ka size compare karne ke liye:

```bash
ls -lh /root/etcbackup.tar*
```

Example Output:

```text
26M  etcbackup.tar
6M   etcbackup.tar.gz
```

Yahan clearly nazar aa raha hai ke compressed archive ka size bohot kam ho gaya hai.

---

# 🔬 Lab 4 – Check Disk Usage

Ek aur tareeqa size dekhne ka:

```bash
du -sh /root/etcbackup.tar
```

Aur compressed archive ke liye:

```bash
du -sh /root/etcbackup.tar.gz
```

Example:

```text
26M
6M
```

---

# Compression Ka Faida

Suppose normal archive ka size tha:

```text
26 MB
```

Compression ke baad size ho gaya:

```text
6 MB
```

Yani file ka size taqreeban **80% tak kam** ho gaya.

Is ka matlab:

- Storage kam use hogi.
- Network transfer tez hoga.
- Backup ka time kam lagega.

---

# Practical Scenario

Suppose aap ke paas:

```text
10 GB
```

ka backup hai.

Agar aap isay direct transfer karein to bohot time lagega.

Best Practice:

### Step 1

Archive banayein.

```text
backup.tar
```

### Step 2

Archive compress karein.

```text
backup.tar.gz
```

### Step 3

Compressed archive ko network ke through transfer karein.

Is tarah transfer bohot tez ho jayega.

---

# 🔬 Practice Exercises

---

## Exercise 1

`/etc` directory ka compressed archive banayein.

```bash
tar -cvzf /root/etcbackup.tar.gz /etc
```

---

## Exercise 2

Archive verify karein.

```bash
ls -ltr /root
```

---

## Exercise 3

Normal aur compressed archive ka size compare karein.

```bash
ls -lh /root/etcbackup.tar*
```

---

## Exercise 4

Disk usage check karein.

```bash
du -sh /root/etcbackup.tar
```

Aur:

```bash
du -sh /root/etcbackup.tar.gz
```

---

## Exercise 5

Compression percentage calculate karein.

Example:

```text
Original Size : 26 MB
Compressed Size : 6 MB
```

Observe karein ke compression ne kitni storage bachayi.

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

Archive create ho gaya lekin compress nahi hua.

Check karein ke command mein `-z` option use hua hai.

Correct Command:

```bash
tar -cvzf backup.tar.gz /etc
```

---

### Scenario 2

Archive ka extension `.tar` hai.

Matlab:

Ye sirf archive hai, compressed nahi.

Compression ke liye extension honi chahiye:

```text
.tar.gz
```

---

### Scenario 3

Network par bohot bari file bhejni hai.

Best Practice:

- Archive create karein.
- gzip se compress karein.
- Phir transfer karein.

---

# 📌 Quick Revision

| Command | Kaam |
|----------|------|
| `tar -cvzf backup.tar.gz /etc` | gzip compressed archive create kare |
| `ls -ltr /root` | Archive verify kare |
| `ls -lh /root/etcbackup.tar*` | Archive sizes compare kare |
| `du -sh archive.tar` | TAR archive ka size dekhe |
| `du -sh archive.tar.gz` | Compressed archive ka size dekhe |

---

# 📖 Key Takeaways

- `-z` option TAR ko gzip compression ke saath use karta hai.
- `.tar.gz` archive normal TAR archive se bohot chhota hota hai.
- Compression storage aur bandwidth dono bachati hai.
- Large backups ko transfer karne se pehle unhein compress karna best practice hai.
- `gzip` Linux mein sab se zyada use hone wala compression method hai.

---

# 💡 Yaad Rakhein

> **TAR Archive ko ek Suitcase aur Compression ko Vacuum Bag samjhein.**
>
> Pehle aap tamam files ko ek suitcase (**TAR Archive**) mein rakhte hain.
>
> Phir us suitcase ko vacuum bag (**gzip Compression**) mein daal dete hain taake us ka size bohot chhota ho jaye.
>
> Is ke baad usay storage mein rakhna ya network ke through transfer karna bohot aasaan aur tez ho jata hai.