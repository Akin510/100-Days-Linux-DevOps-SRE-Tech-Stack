# MODULE 08 – Practice Lab: Prohibit Password-Based SSH Authentication
> **Hands-on Practice Lab – Allow Only SSH Key-Based Authentication**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Disable password-based SSH authentication.
- Allow only SSH key-based authentication.
- Configure the SSH daemon (`sshd`) for public key authentication.
- Understand the `PermitRootLogin` and `PubkeyAuthentication` parameters.
- Reload the SSH service after making configuration changes.
- Prepare the system for passwordless SSH login using SSH keys.

---

# 📖 Introduction

Password-based authentication is the default method used by SSH to authenticate users.

Although it is widely used, it is not the most secure authentication method.

A more secure approach is to use **SSH Public Key Authentication**.

With key-based authentication:

- Passwords are no longer transmitted during login.
- Authentication is performed using a pair of cryptographic keys.
- Brute-force password attacks are greatly reduced.
- Security is significantly improved.

In this lab, we will configure the SSH server to allow **only SSH key-based authentication**.

---

# 🖥️ Lab Scenario

We have a RHEL 9 server.

Currently:

- Direct root login has already been disabled.
- Normal users can still log in using passwords.

Our goal is:

```text
SSH Login
      │
      ▼
Private Key Authentication Only
```

Only users with valid SSH keys should be able to authenticate.

---

# 🔬 Lab 1 – Verify Current SSH Login

First, verify the current SSH behavior.

Try logging in as the root user.

```bash
ssh root@server-ip
```

Expected Result:

```text
Permission denied
```

Root login has already been disabled.

Now test a normal user.

```bash
ssh student@server-ip
```

Enter the user's password.

The login should be successful because password authentication is still enabled.

---

# 🔬 Lab 2 – Switch to the Root User

SSH configuration files require root privileges.

Switch to the root account.

```bash
sudo -i
```

Or:

```bash
su -
```

Verify:

```bash
whoami
```

Expected Output:

```text
root
```

---

# 🔬 Lab 3 – Open the SSH Configuration File

Edit the SSH daemon configuration.

```bash
vim /etc/ssh/sshd_config
```

---

# 🔬 Lab 4 – Configure Root Login

Locate the following parameter.

```text
PermitRootLogin
```

You may find:

```text
#PermitRootLogin prohibit-password
```

Uncomment the line.

The configuration should become:

```text
PermitRootLogin prohibit-password
```

### What Does This Mean?

This setting allows the root account to authenticate **only by using SSH keys**.

Root password authentication is prohibited.

---

# 🔬 Lab 5 – Enable Public Key Authentication

Locate the following parameter.

```text
#PubkeyAuthentication yes
```

Uncomment it.

It should become:

```text
PubkeyAuthentication yes
```

This enables SSH Public Key Authentication.

---

# Understanding the Configuration

| Parameter | Purpose |
|-----------|---------|
| `PermitRootLogin prohibit-password` | Allows root login only with SSH keys |
| `PubkeyAuthentication yes` | Enables SSH key authentication |

---

# 🔬 Lab 6 – Save the Configuration

Save the configuration file and exit the editor.

---

# 🔬 Lab 7 – Validate the Configuration

Before reloading SSH, verify the configuration syntax.

```bash
sshd -t
```

If there is no output, the configuration is valid.

---

# 🔬 Lab 8 – Reload the SSH Service

Reload the SSH daemon.

```bash
systemctl reload sshd
```

Or:

```bash
systemctl reload sshd.service
```

The SSH daemon now uses the updated configuration.

---

# 🔬 Lab 9 – Verify the Effective Configuration

Check the effective SSH configuration.

```bash
sshd -T | grep -Ei "permitrootlogin|pubkeyauthentication"
```

Expected Output:

```text
permitrootlogin prohibit-password
pubkeyauthentication yes
```

---

# 🔬 Lab 10 – Prepare for SSH Key Authentication

At this stage:

- Password authentication for the root account has been disabled.
- Public key authentication has been enabled.

However, users still need to generate SSH key pairs before passwordless authentication can work.

This will be covered in the next practice lab.

---

# Configuration Summary

```text
SSH Client
      │
      ▼
Private Key
      │
Encrypted Authentication
      │
      ▼
SSH Server
      │
Authorized Public Key
      │
      ▼
Login Successful
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Verify the current root login configuration.

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

---

## Exercise 2

Verify that public key authentication is enabled.

```bash
grep PubkeyAuthentication /etc/ssh/sshd_config
```

---

## Exercise 3

Validate the SSH configuration.

```bash
sshd -t
```

---

## Exercise 4

Reload the SSH daemon.

```bash
systemctl reload sshd
```

---

## Exercise 5

Display the effective SSH configuration.

```bash
sshd -T | grep -Ei "permitrootlogin|pubkeyauthentication"
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

SSH service fails to reload.

Check the configuration.

```bash
sshd -t
```

Correct any syntax errors before reloading.

---

### Scenario 2

Public key authentication does not work.

Verify:

```bash
PubkeyAuthentication yes
```

Check the effective configuration.

```bash
sshd -T | grep pubkeyauthentication
```

---

### Scenario 3

Root login still works with a password.

Verify:

```bash
sshd -T | grep permitrootlogin
```

Expected Output:

```text
permitrootlogin prohibit-password
```

---

### Scenario 4

SSH login fails after making configuration changes.

Review the SSH logs.

```bash
journalctl -u sshd
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `vim /etc/ssh/sshd_config` | Edit the SSH server configuration |
| `PermitRootLogin prohibit-password` | Allow root login only with SSH keys |
| `PubkeyAuthentication yes` | Enable SSH key authentication |
| `sshd -t` | Validate SSH configuration |
| `systemctl reload sshd` | Reload SSH configuration |
| `sshd -T` | Display the effective SSH configuration |

---

# 📖 Key Takeaways

- Password-based authentication is less secure than SSH key authentication.
- `PermitRootLogin prohibit-password` disables password authentication for the root account while still allowing SSH key authentication.
- `PubkeyAuthentication yes` enables SSH key-based login.
- Always validate the configuration using `sshd -t` before reloading the service.
- Reload the SSH service after modifying the configuration.
- SSH keys must still be generated and installed before passwordless login can be used.

---

# 💡 Remember

> **Think of SSH keys as a digital ID card instead of a password.**
>
> - A password can be guessed or stolen.
> - A private key is unique and far more secure.
> - The server verifies your identity using your public key without transmitting your password.
>
> **Golden Rule:**
>
> ```text
> SSH Client
>      │
> Private Key
>      │
>      ▼
> Public Key Verification
>      │
>      ▼
> Secure Login
> ```
>
> **Whenever possible, use SSH key authentication instead of password-based authentication for a more secure Linux environment.**