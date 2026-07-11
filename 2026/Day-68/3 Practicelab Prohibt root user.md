# MODULE 08 – Practice Lab: Prohibit Root User Login
> **Hands-on Practice Lab – Disable Direct Root SSH Login**

---

# 🎯 Lab Objective

In this practice lab, you will learn how to:

- Disable direct **root SSH login**.
- Edit the `sshd_config` configuration file.
- Understand the `PermitRootLogin` parameter.
- Reload the SSH service.
- Verify the configuration changes.
- Follow SSH security best practices.

---

# 📖 Introduction

In production Linux environments, allowing **direct root SSH login** is not recommended.

A better approach is to:

- Log in using a **normal user account**.
- Use `sudo` or `su -` when administrative privileges are required.
- This improves both auditing and accountability.

In this lab, we will use the **PermitRootLogin** parameter to prohibit direct root SSH login.

---

# 🖥️ Lab Scenario

We have a Linux server.

The current configuration allows the root user to log in via SSH.

Our goal is:

```text
Root SSH Login
        │
        ▼
Blocked
```

However, normal users should still be able to log in through SSH.

---

# 🔬 Lab 1 – Verify Current Root Login

First, verify that root login is currently allowed.

SSH command:

```bash
ssh root@192.168.56.102
```

Enter the root password.

If the login is successful, it means:

```text
PermitRootLogin yes
```

or another configuration is allowing root login.

---

# 🔬 Lab 2 – Log In as a Normal User

Log in to the server using a normal user account.

Example:

```bash
ssh student@192.168.56.102
```

Or if you are working directly from the console:

```bash
login
```

---

# 🔬 Lab 3 – Elevate Privileges

Only the root user can modify the SSH configuration.

Obtain root privileges.

Using `su`:

```bash
su -
```

Or:

```bash
sudo -i
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

# 🔬 Lab 4 – Edit the SSH Configuration

Open the SSH daemon configuration file.

```bash
vim /etc/ssh/sshd_config
```

Search for:

```text
PermitRootLogin
```

If the line is commented, uncomment it.

Current value:

```text
PermitRootLogin yes
```

Change it to:

```text
PermitRootLogin no
```

Save the file and exit the editor.

---

# 🔬 Lab 5 – Verify the Configuration

Verify the updated configuration.

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

Expected Output:

```text
PermitRootLogin no
```

---

# 🔬 Lab 6 – Validate the Configuration

Before reloading or restarting the service, validate the syntax.

```bash
sshd -t
```

If no output is displayed, the configuration is valid.

---

# 🔬 Lab 7 – Reload the SSH Service

Reload the SSH configuration.

```bash
systemctl reload sshd
```

Or on some systems:

```bash
systemctl reload sshd.service
```

Reloading has the advantage that:

- Existing SSH sessions remain connected.
- Only the configuration is reloaded.

---

# Reload vs Restart

| Command | Purpose |
|----------|---------|
| `systemctl reload sshd` | Reloads the SSH configuration without disconnecting active sessions |
| `systemctl restart sshd` | Completely restarts the SSH service |

In production environments, **reload** is generally preferred when only configuration changes have been made.

---

# 🔬 Lab 8 – Test Root Login Again

Now test root login again.

```bash
ssh root@192.168.56.102
```

Enter the root password.

Expected Result:

```text
Permission denied
```

or

```text
Access denied
```

The root user should no longer be able to log in through SSH.

---

# 🔬 Lab 9 – Verify Normal User Login

Now test logging in as a normal user.

```bash
ssh student@192.168.56.102
```

The login should be successful.

If administrative privileges are required, use:

```bash
sudo -i
```

Or:

```bash
su -
```

---

# 🔬 Lab 10 – Check the Effective SSH Configuration

Verify the effective SSH configuration.

```bash
sshd -T | grep permitrootlogin
```

Expected Output:

```text
permitrootlogin no
```

---

# 🧪 Practice Exercises

---

## Exercise 1

Check the current root login setting.

```bash
grep PermitRootLogin /etc/ssh/sshd_config
```

---

## Exercise 2

Validate the SSH configuration.

```bash
sshd -t
```

---

## Exercise 3

Reload the SSH service.

```bash
systemctl reload sshd
```

---

## Exercise 4

Test root login.

```bash
ssh root@server-ip
```

---

## Exercise 5

Test normal user login.

```bash
ssh student@server-ip
```

---

## Exercise 6

Verify the effective SSH configuration.

```bash
sshd -T | grep permitrootlogin
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1

Root login is still allowed.

Check:

```bash
sshd -T | grep permitrootlogin
```

If the output shows:

```text
permitrootlogin yes
```

Review the SSH configuration again.

---

### Scenario 2

There is a configuration error.

Run:

```bash
sshd -t
```

Correct any syntax errors.

---

### Scenario 3

The SSH service fails to reload.

Check the service status.

```bash
systemctl status sshd
```

Review the logs.

```bash
journalctl -u sshd
```

---

### Scenario 4

Normal users are also unable to log in.

Verify the following parameters in the SSH configuration:

```text
AllowUsers
```

and

```text
PasswordAuthentication
```

---

# 📌 Quick Revision

| Command | Purpose |
|----------|---------|
| `vim /etc/ssh/sshd_config` | Edit the SSH configuration |
| `PermitRootLogin no` | Disable direct root SSH login |
| `grep PermitRootLogin` | Verify the current setting |
| `sshd -t` | Validate the SSH configuration syntax |
| `systemctl reload sshd` | Reload the SSH configuration |
| `sshd -T` | Display the effective SSH configuration |
| `ssh root@server` | Test root SSH login |

---

# 📖 Key Takeaways

- Direct root SSH login should be avoided in production environments.
- `PermitRootLogin no` disables direct root SSH access.
- Always run `sshd -t` after editing the configuration.
- Reloading the SSH service is preferable to restarting when only configuration changes have been made.
- Log in using a normal user account and use `sudo` or `su -` when administrative privileges are needed.
- This approach improves both security and accountability.

---

# 💡 Remember

> **Think of the root account as the Master Key to an office building.**
>
> - Giving everyone the Master Key is not secure.
> - Every administrator should enter the building using their own personal key (normal user account).
> - The Master Key (`sudo` or `su -`) should only be used when administrative tasks are required.
>
> **Golden Rule:**
>
> ```text
> SSH Login
>      │
>      ▼
> Normal User
>      │
>      ▼
> sudo / su -
>      │
>      ▼
> Administrative Tasks
> ```
>
> **Keeping direct Root SSH login disabled is a Linux security best practice.**