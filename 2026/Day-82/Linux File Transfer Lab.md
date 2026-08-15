# Linux File Transfer Lab
## WinSCP, SCP, SFTP, SSH, Permissions, Apache, and rsync

---

# Lab Overview

In this lab, students will learn how to transfer files between:

- A central Linux Development Server
- Their individual Linux servers
- Their local Windows computers

Students will practice:

- SSH
- SCP
- SFTP
- WinSCP
- rsync
- Linux file ownership
- Linux permissions
- Apache web directories
- SELinux contexts

---

# Lab Environment

## Development Server

```text
Hostname: devserver
IP Address: 192.168.1.19
User: nx2026
Home Directory: /home/nx2026
Image Directory: /home/nx2026/images
```

The instructor will place two images on this server:

```text
/home/nx2026/images/image1.jpg
/home/nx2026/images/image2.jpg
```

---

## Student Linux Server

Each student has their own Linux VM.

Example:

```text
Student Server IP: 192.168.1.X
User: nx2026
Home Directory: /home/nx2026
```

Replace:

```text
STUDENT_IP
```

throughout this lab with the IP address of your own Linux server.

For example:

```text
192.168.1.50
```

---

## Windows Computer

Students will also use their local Windows machine for:

- WinSCP
- PowerShell
- Windows OpenSSH/SCP
- Downloading and uploading an image

---

# Network Flow

The lab will follow this general path:

```text
                    DEV SERVER
                   192.168.1.19
                         |
                         |
              +----------+----------+
              |                     |
             SCP                   SFTP
              |                     |
              v                     v
             STUDENT LINUX SERVER
                 192.168.1.X
                       |
                       |
              /home/nx2026
                       |
                       |
                  sudo mv
                       |
                       v
              /var/www/html/images
                       |
                       |
                     HTTP
                       |
                       v
                Windows Browser
```

The second part of the lab reverses the direction:

```text
Windows Computer
      |
      | SCP
      v
Student Linux Server
/home/nx2026/images
      |
      | rsync over SSH
      v
Dev Server
192.168.1.19
/home/nx2026/images/<student-hostname>
```

---

# PART 1 — Instructor Setup

This section is performed on:

```text
Dev Server: 192.168.1.19
```

Log in as root or a user with sudo privileges.

---

## Step 1 — Install Required Software

On Rocky Linux:

```bash
sudo dnf install -y openssh-server rsync
```

Start SSH:

```bash
sudo systemctl enable --now sshd
```

Verify:

```bash
systemctl status sshd
```

---

# Step 2 — Allow SSH Through the Firewall

Check the firewall:

```bash
sudo firewall-cmd --list-all
```

Allow SSH:

```bash
sudo firewall-cmd --permanent --add-service=ssh
```

Reload the firewall:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
sudo firewall-cmd --list-services
```

You should see:

```text
ssh
```

---

# Step 3 — Create the nx2026 User

Create the user:

```bash
sudo useradd -m -s /bin/bash nx2026
```

Set a password:

```bash
sudo passwd nx2026
```

Verify:

```bash
id nx2026
```

Check the home directory:

```bash
ls -ld /home/nx2026
```

---

# Step 4 — Create the Images Directory

Create:

```bash
sudo mkdir -p /home/nx2026/images
```

Give ownership to `nx2026`:

```bash
sudo chown -R nx2026:nx2026 /home/nx2026
```

Set reasonable permissions:

```bash
sudo chmod 700 /home/nx2026
sudo chmod 755 /home/nx2026/images
```

Verify:

```bash
ls -ld /home/nx2026
ls -ld /home/nx2026/images
```

---

# Step 5 — Add Two Website Images

Using the instructor Windows computer, select two instructor-approved web-template images.

Rename them:

```text
image1.jpg
image2.jpg
```

The objective is to eventually have:

```text
/home/nx2026/images/image1.jpg
/home/nx2026/images/image2.jpg
```

You can copy them from Windows using SCP.

Example PowerShell command:

```powershell
scp "$env:USERPROFILE\Downloads\image1.jpg" nx2026@192.168.1.19:/home/nx2026/images/
```

Then:

```powershell
scp "$env:USERPROFILE\Downloads\image2.jpg" nx2026@192.168.1.19:/home/nx2026/images/
```

---

# Step 6 — Verify the Dev Server Images

On the Dev Server:

```bash
ls -lh /home/nx2026/images
```

Expected:

```text
image1.jpg
image2.jpg
```

Check ownership:

```bash
ls -l /home/nx2026/images
```

They should belong to:

```text
nx2026 nx2026
```

If they do not, correct them:

```bash
sudo chown nx2026:nx2026 /home/nx2026/images/*
```

Set the files to normal read permissions:

```bash
sudo chmod 644 /home/nx2026/images/*
```

Final check:

```bash
ls -l /home/nx2026/images
```

---

# PART 2 — Student Linux Server Preparation

Students now work on their own Linux VM.

Log in initially as:

```text
root
```

or another sudo-capable account.

---

# Step 1 — Determine Your Student Server IP

Run:

```bash
ip -br addr
```

or:

```bash
hostname -I
```

Record your IP.

Example:

```text
192.168.1.50
```

For the rest of the lab:

```text
STUDENT_IP = your Linux VM IP address
```

---

# Step 2 — Install Required Packages

Run:

```bash
sudo dnf install -y openssh-server rsync httpd policycoreutils
```

---

# Step 3 — Start SSH

Run:

```bash
sudo systemctl enable --now sshd
```

Verify:

```bash
systemctl status sshd
```

---

# Step 4 — Start Apache

Run:

```bash
sudo systemctl enable --now httpd
```

Verify:

```bash
systemctl status httpd
```

---

# Step 5 — Configure the Firewall

Allow SSH:

```bash
sudo firewall-cmd --permanent --add-service=ssh
```

Allow HTTP:

```bash
sudo firewall-cmd --permanent --add-service=http
```

Reload:

```bash
sudo firewall-cmd --reload
```

Verify:

```bash
sudo firewall-cmd --list-services
```

You should see at least:

```text
ssh http
```

---

# Step 6 — Create the nx2026 User

Run:

```bash
sudo useradd -m -s /bin/bash nx2026
```

Set the password:

```bash
sudo passwd nx2026
```

Verify:

```bash
id nx2026
```

---

# Step 7 — Create an Images Directory

Create:

```bash
sudo mkdir -p /home/nx2026/images
```

Make `nx2026` the owner:

```bash
sudo chown -R nx2026:nx2026 /home/nx2026
```

Set permissions:

```bash
sudo chmod 700 /home/nx2026
sudo chmod 755 /home/nx2026/images
```

Verify:

```bash
ls -ld /home/nx2026
ls -ld /home/nx2026/images
```

---

# PART 3 — SSH Into the Dev Server

Now log in as:

```text
nx2026
```

From your Windows computer or student Linux system:

```bash
ssh nx2026@192.168.1.19
```

Enter the password when requested.

Check where you are:

```bash
pwd
```

Expected:

```text
/home/nx2026
```

List the available images:

```bash
ls -lh /home/nx2026/images
```

You should see:

```text
image1.jpg
image2.jpg
```

---

# PART 4 — Transfer image1.jpg Using SCP

You are currently logged into:

```text
Dev Server: 192.168.1.19
```

We are going to **push** `image1.jpg` from the Dev Server to your Student Server.

The flow is:

```text
Dev Server
   |
   | SCP
   v
Student Server
```

Run:

```bash
scp /home/nx2026/images/image1.jpg nx2026@STUDENT_IP:/home/nx2026/
```

Example:

```bash
scp /home/nx2026/images/image1.jpg nx2026@192.168.1.50:/home/nx2026/
```

Enter the `nx2026` password for your Student Server.

If successful, SCP will display transfer progress similar to:

```text
image1.jpg              100%
```

---

# Understanding the SCP Command

Consider:

```bash
scp /home/nx2026/images/image1.jpg nx2026@192.168.1.50:/home/nx2026/
```

The source is:

```text
/home/nx2026/images/image1.jpg
```

The destination user is:

```text
nx2026
```

The destination server is:

```text
192.168.1.50
```

The destination folder is:

```text
/home/nx2026/
```

The general syntax is:

```text
scp SOURCE USER@SERVER:DESTINATION
```

---

# PART 5 — Transfer image2.jpg Using SFTP

Stay logged into the Dev Server.

Instead of SCP, we will use an interactive SFTP session.

Connect to your Student Server:

```bash
sftp nx2026@STUDENT_IP
```

Example:

```bash
sftp nx2026@192.168.1.50
```

You should receive:

```text
sftp>
```

---

# Step 1 — Check the Remote Directory

Run:

```text
pwd
```

This shows the directory on the **remote Student Server**.

---

# Step 2 — Check the Local Directory

Run:

```text
lpwd
```

The `l` means:

```text
local
```

Because the SFTP client is currently running on the Dev Server, the **local system is the Dev Server**.

---

# Step 3 — Change the Local Directory

Run:

```text
lcd /home/nx2026/images
```

Verify:

```text
lls
```

You should see:

```text
image1.jpg
image2.jpg
```

---

# Step 4 — Change the Remote Directory

Run:

```text
cd /home/nx2026
```

Verify:

```text
pwd
```

---

# Step 5 — Upload image2.jpg

Run:

```text
put image2.jpg
```

You should see transfer progress.

---

# Step 6 — Verify

Run:

```text
ls -l
```

You should now see:

```text
image1.jpg
image2.jpg
```

Remember:

- `image1.jpg` arrived using SCP.
- `image2.jpg` arrived using SFTP.

Exit SFTP:

```text
exit
```

---

# PART 6 — Verify the Files on Your Student Server

Exit the Dev Server:

```bash
exit
```

SSH into your own Student Server:

```bash
ssh nx2026@STUDENT_IP
```

Example:

```bash
ssh nx2026@192.168.1.50
```

Verify:

```bash
pwd
```

Expected:

```text
/home/nx2026
```

Now:

```bash
ls -lh
```

You should have:

```text
image1.jpg
image2.jpg
images/
```

---

# PART 7 — Move the Images Into the Apache Website

Apache normally serves website files from:

```text
/var/www/html
```

> `/var/www/html` is not root's home directory. It is the standard Apache web-content directory and is normally controlled by root.

Because `nx2026` does not normally have permission to write directly to this directory, we use `sudo`.

---

# Step 1 — Create an Apache Images Directory

Run:

```bash
sudo mkdir -p /var/www/html/images
```

---

# Step 2 — Move the Two Images

Run:

```bash
sudo mv /home/nx2026/image1.jpg /var/www/html/images/
```

Then:

```bash
sudo mv /home/nx2026/image2.jpg /var/www/html/images/
```

Or both at once:

```bash
sudo mv /home/nx2026/image1.jpg /home/nx2026/image2.jpg /var/www/html/images/
```

---

# Step 3 — Set Ownership

For a simple static Apache website, root can own these files:

```bash
sudo chown -R root:root /var/www/html/images
```

---

# Step 4 — Set Directory Permissions

Run:

```bash
sudo chmod 755 /var/www/html
sudo chmod 755 /var/www/html/images
```

---

# Step 5 — Set File Permissions

Run:

```bash
sudo chmod 644 /var/www/html/images/image1.jpg
sudo chmod 644 /var/www/html/images/image2.jpg
```

Or:

```bash
sudo chmod 644 /var/www/html/images/*
```

---

# Why 755 for Directories?

A directory such as:

```text
/var/www/html/images
```

needs execute permission so Apache can enter or traverse the directory.

Typical static web-directory permissions are:

```text
755
```

---

# Why 644 for Images?

Apache only needs to read the images.

Typical static-file permissions are:

```text
644
```

This means:

```text
Owner:  read + write
Group:  read
Others: read
```

---

# PART 8 — Fix SELinux Context

This step is important on Rocky Linux.

Because we used:

```bash
mv
```

to move files from:

```text
/home/nx2026
```

to:

```text
/var/www/html
```

the files might retain their original SELinux security context.

Check:

```bash
ls -lZ /var/www/html/images
```

Run:

```bash
sudo restorecon -Rv /var/www/html
```

Check again:

```bash
ls -lZ /var/www/html/images
```

The files should now have an Apache-compatible SELinux type such as:

```text
httpd_sys_content_t
```

---

# PART 9 — Create a Simple Web Page

Create:

```bash
sudo vi /var/www/html/index.html
```

Add:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Linux File Transfer Lab</title>
</head>

<body>

<h1>Linux SCP, SFTP and WinSCP Lab</h1>

<h2>Image transferred using SCP</h2>
<img src="images/image1.jpg" width="500">

<h2>Image transferred using SFTP</h2>
<img src="images/image2.jpg" width="500">

</body>
</html>
```

Save the file.

Set permissions:

```bash
sudo chmod 644 /var/www/html/index.html
```

Set SELinux context:

```bash
sudo restorecon -v /var/www/html/index.html
```

---

# PART 10 — Test Apache Locally

Run:

```bash
curl http://localhost
```

You should see your HTML.

You can also run:

```bash
curl http://127.0.0.1
```

---

# PART 11 — Test From Windows

Open a Windows browser.

Enter:

```text
http://STUDENT_IP
```

Example:

```text
http://192.168.1.50
```

You should see:

```text
Linux SCP, SFTP and WinSCP Lab
```

along with the two images.

---

# PART 12 — Learn WinSCP

Now we will perform file transfer using a graphical Windows application.

Open:

```text
WinSCP
```

Create a new connection.

Use:

```text
File protocol: SFTP
Host name: STUDENT_IP
Port: 22
Username: nx2026
Password: your nx2026 password
```

Example:

```text
File protocol: SFTP
Host name: 192.168.1.50
Port: 22
Username: nx2026
```

Click:

```text
Login
```

---

# Understanding the WinSCP Screen

WinSCP normally displays two panels.

The left side represents:

```text
Windows
```

The right side represents:

```text
Linux Server
```

Conceptually:

```text
+----------------------+-------------------------+
| Windows PC           | Linux Server            |
+----------------------+-------------------------+
| C:\Users\Student     | /home/nx2026            |
| Downloads            | /var/www/html/images    |
| Desktop              | images                  |
+----------------------+-------------------------+
```

---

# PART 13 — Download an Image Using WinSCP

On the Linux side of WinSCP, navigate to:

```text
/var/www/html/images
```

You should see:

```text
image1.jpg
image2.jpg
```

On the Windows side, navigate to:

```text
Downloads
```

Drag:

```text
image1.jpg
```

from the Linux side to the Windows side.

You have now performed:

```text
Linux Server
     |
     | SFTP through WinSCP
     v
Windows Computer
```

Verify that the image exists in Windows Downloads.

---

# PART 14 — Download a New Image to Windows

Using your Windows web browser, download another instructor-approved image.

Rename it:

```text
windows-image.jpg
```

Save it in:

```text
C:\Users\<your-user>\Downloads
```

---

# PART 15 — Upload the Windows Image Using Command-Line SCP

We will **not use WinSCP** for this transfer.

Instead, open:

```text
Windows PowerShell
```

---

# Step 1 — Verify SCP Is Available

Run:

```powershell
scp
```

or:

```powershell
scp -V
```

If Windows recognizes the command, continue.

You can also check SSH:

```powershell
ssh -V
```

---

# Step 2 — Upload the Image

First make sure the destination folder exists.

SSH to the student server:

```powershell
ssh nx2026@STUDENT_IP
```

Run:

```bash
mkdir -p /home/nx2026/images
```

Exit:

```bash
exit
```

Now from Windows PowerShell:

```powershell
scp "$env:USERPROFILE\Downloads\windows-image.jpg" nx2026@STUDENT_IP:/home/nx2026/images/
```

Example:

```powershell
scp "$env:USERPROFILE\Downloads\windows-image.jpg" nx2026@192.168.1.50:/home/nx2026/images/
```

Enter your Linux password.

---

# PART 16 — Verify the Windows-to-Linux Transfer

SSH into your Student Server:

```powershell
ssh nx2026@STUDENT_IP
```

Run:

```bash
ls -lh /home/nx2026/images
```

You should see:

```text
windows-image.jpg
```

Check ownership:

```bash
ls -l /home/nx2026/images
```

Because `nx2026` uploaded the image, it should normally be owned by:

```text
nx2026 nx2026
```

If necessary, an administrator could correct ownership with:

```bash
sudo chown nx2026:nx2026 /home/nx2026/images/windows-image.jpg
```

Set normal file permissions:

```bash
chmod 644 /home/nx2026/images/windows-image.jpg
```

---

# PART 17 — Prevent Students From Overwriting Each Other's Files

All students are using the same account on the Dev Server:

```text
nx2026
```

Therefore, we do not want every student uploading a file named:

```text
windows-image.jpg
```

Rename your image using your hostname.

Run:

```bash
hostname
```

Example:

```text
student05
```

Now rename the image:

```bash
mv /home/nx2026/images/windows-image.jpg \
/home/nx2026/images/$(hostname)-windows-image.jpg
```

Verify:

```bash
ls -lh /home/nx2026/images
```

Example:

```text
student05-windows-image.jpg
```

---

# PART 18 — Use rsync to Send the Image Back to the Dev Server

Now we will transfer the image in the opposite direction.

The flow will be:

```text
Student Linux Server
192.168.1.X
       |
       | rsync over SSH
       v
Dev Server
192.168.1.19
```

---

# Step 1 — Create Your Personal Submission Directory

From your Student Server:

```bash
ssh nx2026@192.168.1.19 "mkdir -p /home/nx2026/images/$(hostname)"
```

Enter the Dev Server password.

For a server named:

```text
student05
```

this creates:

```text
/home/nx2026/images/student05
```

on the Dev Server.

---

# Step 2 — Transfer Using rsync

Run:

```bash
rsync -av --progress /home/nx2026/images/ \
nx2026@192.168.1.19:/home/nx2026/images/$(hostname)/
```

You should see your file being transferred.

Example:

```text
sending incremental file list

student05-windows-image.jpg

sent ...
received ...
```

---

# Understanding the rsync Command

The command:

```bash
rsync -av --progress SOURCE/ USER@SERVER:DESTINATION/
```

contains:

```text
rsync
```

The program performing synchronization.

```text
-a
```

Archive mode.

```text
-v
```

Verbose output.

```text
--progress
```

Shows transfer progress.

The source is:

```text
/home/nx2026/images/
```

The remote destination is:

```text
nx2026@192.168.1.19:/home/nx2026/images/<hostname>/
```

---

# PART 19 — Verify the File on the Dev Server

SSH to the Dev Server:

```bash
ssh nx2026@192.168.1.19
```

Run:

```bash
ls -lh /home/nx2026/images/$(hostname)
```

However, remember that once you SSH into the Dev Server:

```bash
$(hostname)
```

now represents the **Dev Server's hostname**, not your Student Server's hostname.

Therefore it is better to manually specify your Student Server name.

Example:

```bash
ls -lh /home/nx2026/images/student05
```

You should see:

```text
student05-windows-image.jpg
```

---

# Important rsync Teaching Point

Compare SCP:

```bash
scp file.jpg server:/directory/
```

with rsync:

```bash
rsync -av file.jpg server:/directory/
```

SCP is excellent for straightforward file copying.

rsync is particularly useful when synchronizing directories because it can determine which files need to be transferred rather than blindly treating every synchronization as a completely new directory copy.

---

# PART 20 — Optional Final Dev Server Publishing Exercise

This section should only be performed if students have appropriate sudo access on the Dev Server.

Otherwise, the instructor can perform it.

Suppose the student's submission is:

```text
/home/nx2026/images/student05/student05-windows-image.jpg
```

Create a Dev Server web directory:

```bash
sudo mkdir -p /var/www/html/student-images
```

Copy the student's image:

```bash
sudo cp /home/nx2026/images/student05/student05-windows-image.jpg \
/var/www/html/student-images/
```

Set ownership:

```bash
sudo chown root:root /var/www/html/student-images/student05-windows-image.jpg
```

Set permissions:

```bash
sudo chmod 755 /var/www/html/student-images
sudo chmod 644 /var/www/html/student-images/student05-windows-image.jpg
```

Correct the SELinux context:

```bash
sudo restorecon -Rv /var/www/html/student-images
```

Verify:

```bash
ls -lZ /var/www/html/student-images
```

The image can then be accessed using:

```text
http://192.168.1.19/student-images/student05-windows-image.jpg
```

provided Apache and the firewall are configured on the Dev Server.

---

# PART 21 — Complete Lab File-Transfer Map

By the end of the lab, students have performed all of these transfers:

```text
1. DEV SERVER → STUDENT SERVER

   image1.jpg
   SCP
   ---------------------------->

2. DEV SERVER → STUDENT SERVER

   image2.jpg
   SFTP
   ---------------------------->

3. STUDENT SERVER → WINDOWS

   image1.jpg
   WinSCP / SFTP
   ---------------------------->

4. WINDOWS → STUDENT SERVER

   windows-image.jpg
   SCP
   ---------------------------->

5. STUDENT SERVER → DEV SERVER

   studentXX-windows-image.jpg
   rsync over SSH
   ---------------------------->
```

---

# PART 22 — Commands Students Should Know

## SSH

Connect to another server:

```bash
ssh USER@SERVER
```

Example:

```bash
ssh nx2026@192.168.1.19
```

---

## SCP — Send a File

```bash
scp FILE USER@SERVER:/destination/
```

Example:

```bash
scp image.jpg nx2026@192.168.1.19:/home/nx2026/
```

---

## SCP — Download a File

```bash
scp USER@SERVER:/path/file .
```

Example:

```bash
scp nx2026@192.168.1.19:/home/nx2026/images/image1.jpg .
```

The:

```text
.
```

means:

```text
current directory
```

---

## SFTP

Connect:

```bash
sftp USER@SERVER
```

Example:

```bash
sftp nx2026@192.168.1.19
```

Useful SFTP commands:

```text
pwd
ls
cd
lpwd
lls
lcd
get
put
exit
```

---

## SFTP — Download

```text
get filename
```

---

## SFTP — Upload

```text
put filename
```

---

## rsync

Basic remote synchronization:

```bash
rsync -av SOURCE/ USER@SERVER:DESTINATION/
```

Example:

```bash
rsync -av ~/images/ nx2026@192.168.1.19:/home/nx2026/images/student01/
```

---

# PART 23 — Understanding `local` and `remote`

This is extremely important when learning file-transfer commands.

If you are logged into the Dev Server and run:

```bash
sftp nx2026@192.168.1.50
```

then:

```text
LOCAL  = Dev Server
REMOTE = Student Server
```

Inside SFTP:

```text
ls
```

shows the remote server.

```text
lls
```

shows the local server.

```text
pwd
```

shows the remote directory.

```text
lpwd
```

shows the local directory.

```text
cd
```

changes the remote directory.

```text
lcd
```

changes the local directory.

---

# PART 24 — Permissions Review

## User Home Directory

```text
/home/nx2026
```

Recommended for this lab:

```bash
chmod 700 /home/nx2026
```

Meaning:

```text
Owner: full access
Group: no access
Others: no access
```

---

## Images Directory

```text
/home/nx2026/images
```

Can use:

```bash
chmod 755 /home/nx2026/images
```

---

## Apache Directories

```text
/var/www/html
/var/www/html/images
```

Use:

```bash
chmod 755
```

---

## Static Website Files

Images and HTML files:

```bash
chmod 644 filename
```

---

# PART 25 — Troubleshooting

## Problem 1 — Connection Refused

Example:

```text
ssh: connect to host 192.168.1.50 port 22: Connection refused
```

Check SSH:

```bash
sudo systemctl status sshd
```

Start it:

```bash
sudo systemctl enable --now sshd
```

---

# Problem 2 — No Route to Host

Check:

```bash
ping STUDENT_IP
```

Check the IP:

```bash
ip -br addr
```

Check firewall configuration:

```bash
sudo firewall-cmd --list-all
```

---

# Problem 3 — Permission Denied

Check ownership:

```bash
ls -ld /home/nx2026
```

Correct:

```bash
sudo chown -R nx2026:nx2026 /home/nx2026
```

---

# Problem 4 — Cannot Copy Into /var/www/html

This will normally fail:

```bash
mv image1.jpg /var/www/html/
```

because `nx2026` is not root.

Use:

```bash
sudo mv image1.jpg /var/www/html/
```

---

# Problem 5 — Image Exists But Apache Cannot Display It

Check standard permissions:

```bash
ls -l /var/www/html/images
```

Then check SELinux:

```bash
ls -lZ /var/www/html/images
```

Correct the contexts:

```bash
sudo restorecon -Rv /var/www/html
```

---

# Problem 6 — rsync Command Not Found

Install it:

```bash
sudo dnf install -y rsync
```

Remember that when using rsync between two Linux servers, having rsync installed on both systems avoids remote-execution problems.

---

# Problem 7 — `scp` Not Found on Windows

Open PowerShell as Administrator.

Check the OpenSSH Client:

```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH.Client*'
```

If it is not installed:

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

Close and reopen PowerShell.

Test:

```powershell
ssh -V
```

Then:

```powershell
scp
```

---

# PART 26 — Student Lab Questions

Answer the following questions after completing the lab.

### Question 1

What protocol does SCP use to securely transfer files?

---

### Question 2

What TCP port is normally used by SSH, SCP, and SFTP?

---

### Question 3

What was the SCP command you used to transfer `image1.jpg`?

---

### Question 4

What was the SFTP command used to upload `image2.jpg`?

---

### Question 5

Inside SFTP, what is the difference between:

```text
ls
```

and:

```text
lls
```

---

### Question 6

What is the difference between:

```text
cd
```

and:

```text
lcd
```

inside SFTP?

---

### Question 7

Why could `nx2026` not directly write into:

```text
/var/www/html
```

without sudo?

---

### Question 8

Why were the image files given:

```text
644
```

permissions?

---

### Question 9

Why were the web directories given:

```text
755
```

permissions?

---

### Question 10

Why did we run:

```bash
restorecon -Rv /var/www/html
```

after moving files from `/home/nx2026`?

---

### Question 11

Which program allowed you to transfer files using a graphical Windows interface?

```text
____________________
```

---

### Question 12

Which method did you use for each transfer?

```text
image1.jpg
Dev Server → Student Server:

____________________
```

```text
image2.jpg
Dev Server → Student Server:

____________________
```

```text
image1.jpg
Student Server → Windows:

____________________
```

```text
windows-image.jpg
Windows → Student Server:

____________________
```

```text
windows-image.jpg
Student Server → Dev Server:

____________________
```

---

# PART 27 — Expected Answers

```text
image1.jpg
Dev → Student
= SCP
```

```text
image2.jpg
Dev → Student
= SFTP
```

```text
image1.jpg
Student → Windows
= WinSCP using SFTP
```

```text
windows-image.jpg
Windows → Student
= SCP
```

```text
studentXX-windows-image.jpg
Student → Dev
= rsync over SSH
```

---

# Final Lab Objective

After completing this lab, students should understand the complete file-transfer workflow:

```text
SSH
 |
 +---- SCP
 |
 +---- SFTP
 |
 +---- WinSCP
 |
 +---- rsync
```

Students should also understand that successful file transfer involves more than simply copying a file.

They must understand:

```text
Network connectivity
        +
SSH authentication
        +
Correct source/destination
        +
Linux ownership
        +
Linux permissions
        +
SELinux
        +
Application permissions
        =
Successful File Transfer
```

---

# Lab Complete

You have successfully transferred files:

```text
Linux → Linux using SCP
Linux → Linux using SFTP
Linux → Windows using WinSCP
Windows → Linux using SCP
Linux → Linux using rsync
```

You have also published transferred files through Apache and applied the appropriate Linux ownership, permissions, and SELinux contexts.