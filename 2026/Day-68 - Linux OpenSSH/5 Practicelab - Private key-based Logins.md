# MODULE 08 – Practice Lab: SSH Private Key-Based Login
> **Hands-on Practice Lab – Configure SSH Private Key Authentication for the Root User**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Generate an SSH public/private key pair.
- Create an RSA key pair using `ssh-keygen`.
- Rename the private key to `.pem` format.
- Configure the `.ssh` directory.
- Create the `authorized_keys` file.
- Copy the public key into `authorized_keys`.
- Reload the SSH service.
- Configure an SSH client (MobaXterm or PuTTY) to use the private key.
- Log in to a Linux server without entering a password.

---

# 📖 Introduction

SSH Key-Based Authentication is one of the most secure methods of logging into a Linux system.

Instead of typing a password every time you connect, SSH uses two cryptographic keys:

- **Private Key** – Kept securely on the client machine.
- **Public Key** – Stored on the Linux server.

During authentication:

1. The client proves ownership of the private key.
2. The server verifies it against the stored public key.
3. If they match, login is granted without requiring a password.

In this lab, we will configure SSH key-based authentication for the **root** user.

---

# 🖥️ Lab Scenario

We have a Linux server.

Our goal is:

```text
Windows Client
        │
        │  Private Key (.pem)
        ▼
Linux Server
        │
Authorized Public Key
        ▼
Passwordless Root Login
```

---

# 🔬 Lab 1 – Navigate to the Root Home Directory

Switch to the root account if necessary.

```bash
sudo -i
```

Go to the root user's home directory.

```bash
cd /root
```

Verify your location.

```bash
pwd
```

Expected Output:

```text
/root
```

---

# 🔬 Lab 2 – Create a Directory for the SSH Keys

Create a directory to store the key pair.

```bash
mkdir keys
```

You may choose any directory name.

Move into the directory.

```bash
cd keys
```

---

# 🔬 Lab 3 – Generate the SSH Key Pair

Generate an RSA SSH key pair.

```bash
ssh-keygen -t rsa
```

You may specify a custom filename if desired.

Example:

```bash
ssh-keygen -t rsa -f root_key
```

This creates two files:

```text
root_key
root_key.pub
```

---

# Understanding the Generated Files

| File | Purpose |
|------|---------|
| `root_key` | Private Key |
| `root_key.pub` | Public Key |

The private key must remain secret.

The public key will be copied to the Linux server.

---

# 🔬 Lab 4 – Rename the Private Key

Rename the private key so that it has a `.pem` extension.

Example:

```bash
mv root_key root_key.pem
```

The `.pem` extension is commonly used by SSH clients such as:

- MobaXterm
- PuTTY
- AWS EC2
- OpenSSH Clients

---

# 🔬 Lab 5 – Create the `.ssh` Directory

Return to the root home directory.

```bash
cd /root
```

Create the `.ssh` directory.

```bash
mkdir .ssh
```

Set the correct permissions.

```bash
chmod 700 .ssh
```

---

# Why Permission 700?

Permission **700** means:

- Owner: Read, Write, Execute
- Group: No Access
- Others: No Access

SSH requires secure permissions for the `.ssh` directory.

---

# 🔬 Lab 6 – Create the `authorized_keys` File

Move into the `.ssh` directory.

```bash
cd .ssh
```

Create the authorization file.

```bash
touch authorized_keys
```

This file stores the public keys that are allowed to log in.

---

# 🔬 Lab 7 – Copy the Public Key

Return to the key directory.

```bash
cd /root/keys
```

Copy the public key into the `authorized_keys` file.

Example:

```bash
cat root_key.pub >> /root/.ssh/authorized_keys
```

Verify the contents.

```bash
cat /root/.ssh/authorized_keys
```

The contents of the public key should now appear in the file.

---

# Authentication Flow

```text
Private Key
      │
      ▼
Client
      │
Encrypted Authentication
      │
      ▼
Server
      │
authorized_keys
      │
      ▼
Login Successful
```

---

# 🔬 Lab 8 – Reload the SSH Service

Reload the SSH daemon.

```bash
systemctl reload sshd
```

The new SSH configuration is now active.

---

# 🔬 Lab 9 – Copy the Private Key to Windows

The private key must be available on the Windows client.

You can copy it using any preferred method, such as:

- WinSCP
- WinZip
- SCP
- Copy and Paste into Notepad

Save the file as:

```text
root_key.pem
```

Example folder:

```text
E:\Keys\
```

---

# 🔬 Lab 10 – Configure MobaXterm

Open **MobaXterm**.

Create a new SSH session.

Configure:

Server:

```text
Server IP Address
```

Username:

```text
root
```

Open:

```text
Advanced SSH Settings
```

Enable:

```text
Use Private Key
```

Browse and select:

```text
root_key.pem
```

Click **OK**.

---

# Expected Result

Instead of asking for a password,

the server authenticates using the private key.

Login is successful.

---

# Password Login vs SSH Key Login

## Password Authentication

```text
Client
    │
Password
    ▼
Server
```

---

## SSH Key Authentication

```text
Client
    │
Private Key
    ▼
Encrypted Authentication
    ▼
Server
    │
authorized_keys
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Generate a new RSA key pair.

```bash
ssh-keygen -t rsa
```

---

## Exercise 2

Create the `.ssh` directory.

```bash
mkdir ~/.ssh
chmod 700 ~/.ssh
```

---

## Exercise 3

Create the `authorized_keys` file.

```bash
touch ~/.ssh/authorized_keys
```

---

## Exercise 4

Append the public key.

```bash
cat id_rsa.pub >> ~/.ssh/authorized_keys
```

---

## Exercise 5

Reload the SSH service.

```bash
systemctl reload sshd
```

---

## Exercise 6

Log in using your private key from your SSH client.

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

SSH still prompts for a password.

Verify that the public key exists.

```bash
cat ~/.ssh/authorized_keys
```

---

### Scenario 2

The `.ssh` directory has incorrect permissions.

Fix them.

```bash
chmod 700 ~/.ssh
```

---

### Scenario 3

The `authorized_keys` file has incorrect permissions.

Fix them.

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

### Scenario 4

The SSH service is not using the updated configuration.

Reload it.

```bash
systemctl reload sshd
```

---

### Scenario 5

The private key is not selected in MobaXterm.

Open:

```text
Advanced SSH Settings
```

Enable:

```text
Use Private Key
```

Select the correct `.pem` file.

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `ssh-keygen -t rsa` | Generate an RSA SSH key pair |
| `mkdir .ssh` | Create the SSH configuration directory |
| `chmod 700 .ssh` | Secure the `.ssh` directory |
| `touch authorized_keys` | Create the authorized keys file |
| `cat key.pub >> authorized_keys` | Copy the public key into the authorized keys file |
| `systemctl reload sshd` | Reload the SSH service |
| `chmod 600 authorized_keys` | Secure the authorized keys file |

---

# 📖 Key Takeaways

- SSH Key Authentication is more secure than password authentication.
- Every key pair consists of a private key and a matching public key.
- The **private key** must never be shared.
- The **public key** is copied into the `authorized_keys` file.
- The `.ssh` directory should have **700** permissions.
- The `authorized_keys` file should have **600** permissions.
- After configuration, users can log in without entering a password.

---

# 💡 Remember

> **Think of SSH authentication like a secure office building.**
>
> - The **Private Key** is your personal security badge.
> - The **Public Key** is the list of approved badges stored at the entrance.
> - When you arrive, the security guard compares your badge with the approved list.
> - If they match, the door opens automatically.
>
> **Golden Rule:**
>
> ```text
> Private Key
>      │
>      ▼
> Never Share It
>      │
>      ▼
> Public Key
>      │
>      ▼
> authorized_keys
>      │
>      ▼
> Secure Passwordless Login
> ```
>
> **Protect your private key at all times. Anyone who possesses it may be able to access your servers.**