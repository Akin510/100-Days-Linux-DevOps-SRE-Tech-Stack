# MODULE 08 – OpenSSH
> **Configuring and Securing Remote Linux Communication**

---

# 🎯 Learning Objectives

In this module, you will learn:

- What OpenSSH is.
- The difference between OpenSSH and the SSH protocol.
- Why SSH is more secure than Telnet and FTP.
- The default SSH port number.
- How to connect from one Linux server to another.
- How to connect using the same username.
- How to connect using a different username.
- How SSH host-key verification works.
- How to check logged-in users.
- How to identify your current system and working directory.
- How to exit a remote SSH session.

---

# 📖 Introduction

In an environment with multiple Linux servers and many users, systems frequently communicate with one another.

Examples include:

- Administrators remotely managing servers.
- Users accessing remote Linux systems.
- Scripts transferring files between servers.
- Applications communicating across a network.
- Support engineers troubleshooting remote machines.

This communication must be secure.

Linux commonly uses **OpenSSH** and the **SSH protocol** to provide encrypted remote access.

---

# 1. What Is OpenSSH?

OpenSSH is:

> **A free and open-source collection of programs used to provide secure and encrypted communication over a computer network.**

OpenSSH uses the:

```text
SSH Protocol
```

SSH means:

> **Secure Shell**

OpenSSH provides tools for:

- Remote login
- Remote command execution
- Secure file transfer
- Key-based authentication
- Secure tunneling
- Port forwarding

---

# 2. OpenSSH vs SSH

OpenSSH and SSH are related, but they are not the same thing.

| Item | Meaning |
|------|---------|
| SSH | A secure network protocol |
| OpenSSH | A collection of programs that implements the SSH protocol |

Therefore:

> **SSH is the protocol, while OpenSSH is the software implementation.**

---

# 3. Default SSH Port

The default SSH port is:

```text
22
```

> **Important correction:** SSH does not use port `32`. The standard default port is `22`.

You can verify the SSH service port using:

```bash
ss -tulpn | grep :22
```

or:

```bash
grep -i '^Port' /etc/ssh/sshd_config
```

If no custom port is configured, SSH normally listens on port `22`.

---

# 4. OpenSSH Packages

On many Linux server installations, OpenSSH packages are installed by default.

The main packages are commonly:

```text
openssh
openssh-clients
openssh-server
```

Check installed packages:

```bash
rpm -qa | grep openssh
```

Example Output:

```text
openssh-8.x
openssh-clients-8.x
openssh-server-8.x
```

---

# Install OpenSSH If Required

On Rocky Linux, Red Hat Enterprise Linux, or AlmaLinux:

```bash
sudo dnf install -y openssh openssh-clients openssh-server
```

---

# 5. Main OpenSSH Components

| Component | Purpose |
|-----------|---------|
| `ssh` | Connect to a remote SSH server |
| `sshd` | SSH server daemon |
| `scp` | Securely copy files |
| `sftp` | Secure file transfer |
| `ssh-keygen` | Create SSH key pairs |
| `ssh-copy-id` | Copy a public key to a remote server |
| `ssh-agent` | Store private-key credentials temporarily |
| `ssh-add` | Add keys to the SSH agent |

---

# 6. Why Is SSH Secure?

SSH protects network communication using encryption.

It helps protect:

- Usernames
- Passwords
- Commands
- Session data
- File transfers
- Authentication information

Without encryption, attackers may be able to read transmitted information.

---

# 7. SSH vs Telnet

Telnet is considered insecure because it normally sends information in plain text.

| Feature | SSH | Telnet |
|---------|-----|--------|
| Encrypted communication | Yes | No |
| Secure authentication | Yes | No |
| Default port | `22` | `23` |
| Recommended for remote administration | Yes | No |
| Password protection | Encrypted | Plain text |

SSH should be used instead of Telnet for remote server access.

---

# 8. SSH vs FTP

Traditional FTP is also not encrypted by default.

| Feature | SSH/SFTP/SCP | Traditional FTP |
|---------|--------------|-----------------|
| Credentials encrypted | Yes | No |
| File data encrypted | Yes | No |
| Secure remote shell | Yes | No |
| Recommended on untrusted networks | Yes | No |

For secure file transfers, use:

```text
scp
```

or:

```text
sftp
```

---

# 9. SSH Security and Man-in-the-Middle Protection

SSH uses **host keys** to identify remote servers.

When connecting to a server for the first time, SSH may display:

```text
The authenticity of host 'server2' cannot be established.
```

It also displays the server's host-key fingerprint.

Example:

```text
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxx
```

SSH asks:

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Before typing `yes`, the server fingerprint should ideally be verified through a trusted source.

---

# What Happens After You Accept the Host Key?

When you type:

```text
yes
```

the host key is saved in:

```text
~/.ssh/known_hosts
```

Future connections compare the server's presented key with the saved key.

If the key unexpectedly changes, SSH displays a warning such as:

```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

This may indicate:

- The server was reinstalled.
- SSH host keys were regenerated.
- The IP address now belongs to another server.
- A man-in-the-middle attack may be occurring.

Never ignore this warning without verifying the reason.

---

# 10. Practice Lab Environment

For this practice lab, assume there are two Linux servers:

| Server | Example Hostname | Example IP |
|--------|------------------|------------|
| Server 1 | `server1` | `192.168.1.66` |
| Server 2 | `server2` | `192.168.1.67` |

Two users will be created on both servers:

```text
myuser1
myuser2
```

> Replace the example IP addresses with the actual IP addresses in your lab.

---

# 11. Check the Server Hostname

On Server 1:

```bash
hostname
```

Example Output:

```text
server1
```

On Server 2:

```bash
hostname
```

Example Output:

```text
server2
```

For detailed information:

```bash
hostnamectl
```

---

# 12. Check the Server IP Address

Use:

```bash
ip address
```

Short version:

```bash
ip a
```

To display IPv4 addresses only:

```bash
ip -4 addr
```

You can also use:

```bash
hostname -I
```

Example Output:

```text
192.168.1.67
```

> The older `ifconfig` command may not be installed by default. The modern command is `ip`.

---

# 13. Create Users on Server 1

Log in as `root` or use `sudo`.

Create the users:

```bash
sudo useradd myuser1
sudo useradd myuser2
```

Set passwords:

```bash
sudo passwd myuser1
sudo passwd myuser2
```

Verify:

```bash
id myuser1
id myuser2
```

---

# 14. Create the Same Users on Server 2

On Server 2:

```bash
sudo useradd myuser1
sudo useradd myuser2
```

Set passwords:

```bash
sudo passwd myuser1
sudo passwd myuser2
```

Verify:

```bash
id myuser1
id myuser2
```

---

# Important Note About User Accounts

The same username does not automatically mean the accounts are identical.

Each server maintains its own:

- User database
- Password
- UID
- Group memberships
- Home directory
- SSH keys

The user must exist on the destination server for a normal user login.

---

# 15. Check the SSH Server Service

On Server 2, verify the SSH daemon:

```bash
systemctl status sshd
```

If it is not running:

```bash
sudo systemctl enable --now sshd
```

Verify again:

```bash
systemctl is-active sshd
```

Expected Output:

```text
active
```

---

# 16. Check the Firewall

On Server 2, verify the SSH firewall rule:

```bash
sudo firewall-cmd --list-services
```

You should see:

```text
ssh
```

If SSH is not allowed:

```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

Verify:

```bash
sudo firewall-cmd --list-services
```

---

# 17. Test Network Connectivity

From Server 1, test Server 2:

```bash
ping -c 4 192.168.1.67
```

Test the SSH port:

```bash
nc -zv 192.168.1.67 22
```

If `nc` is unavailable:

```bash
timeout 3 bash -c '</dev/tcp/192.168.1.67/22' && echo "SSH port is open"
```

---

# 18. Switch to `myuser1` on Server 1

Run:

```bash
su - myuser1
```

The hyphen (`-`) starts a login shell and loads the user's environment.

Verify:

```bash
whoami
```

Expected Output:

```text
myuser1
```

Check the current directory:

```bash
pwd
```

Expected Output:

```text
/home/myuser1
```

---

# 19. SSH Using the Same Username

While logged in as `myuser1` on Server 1, run:

```bash
ssh 192.168.1.67
```

Because no username was specified, SSH uses the current local username:

```text
myuser1
```

This is equivalent to:

```bash
ssh myuser1@192.168.1.67
```

---

# First Connection Prompt

You may see:

```text
The authenticity of host '192.168.1.67' cannot be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

After verifying the fingerprint, type:

```text
yes
```

Then enter the password for:

```text
myuser1
```

on Server 2.

---

# 20. Verify the Remote Login

After connecting, run:

```bash
whoami
```

Expected Output:

```text
myuser1
```

Check the hostname:

```bash
hostname
```

Expected Output:

```text
server2
```

Check the current directory:

```bash
pwd
```

Expected Output:

```text
/home/myuser1
```

This confirms that:

- You are on Server 2.
- You are logged in as `myuser1`.
- You are in the remote user's home directory.

---

# 21. Check Logged-In Users with `w`

Run:

```bash
w
```

Example Output:

```text
USER      TTY      FROM             LOGIN@   IDLE   JCPU   PCPU  WHAT
root      tty1     -                09:10    1:20   0.04s  0.04s -bash
myuser1   pts/0    192.168.1.66     09:30    0.00s  0.03s  0.01s w
```

---

# Understanding `w` Output

| Column | Meaning |
|--------|---------|
| `USER` | Logged-in username |
| `TTY` | Terminal associated with the session |
| `FROM` | Remote source address or hostname |
| `LOGIN@` | Login time |
| `IDLE` | Inactive time |
| `JCPU` | CPU time used by session-related processes |
| `PCPU` | CPU time used by the current process |
| `WHAT` | Current command |

---

# 22. Physical and Pseudo Terminals

Linux uses different terminal types.

| Terminal | Description |
|----------|-------------|
| `tty1`, `tty2` | Local text console |
| `pts/0`, `pts/1` | Pseudo terminal used by SSH, PuTTY, or terminal emulators |

An SSH session commonly appears as:

```text
pts/0
```

A direct console login may appear as:

```text
tty1
```

---

# 23. Exit the Remote SSH Session

To leave Server 2 and return to Server 1:

```bash
exit
```

You may also press:

```text
Ctrl + D
```

Verify that you are back on Server 1:

```bash
hostname
```

Expected Output:

```text
server1
```

---

# 24. SSH Using a Different Username

Suppose you are logged in as:

```text
myuser1
```

on Server 1, but you want to connect to Server 2 as:

```text
myuser2
```

Use:

```bash
ssh myuser2@192.168.1.67
```

Enter the password for `myuser2` on Server 2.

---

# Verify the Remote Identity

Run:

```bash
whoami
```

Expected Output:

```text
myuser2
```

Run:

```bash
pwd
```

Expected Output:

```text
/home/myuser2
```

Run:

```bash
hostname
```

Expected Output:

```text
server2
```

---

# 25. SSH Username Rules

## When No Username Is Specified

Command:

```bash
ssh 192.168.1.67
```

SSH attempts to use the current local username.

Example:

```text
Local user: myuser1
Remote login attempt: myuser1
```

---

## When a Username Is Specified

Command:

```bash
ssh myuser2@192.168.1.67
```

SSH attempts to use:

```text
myuser2
```

on the remote server.

---

# SSH Login Syntax

```bash
ssh username@server
```

Examples:

```bash
ssh myuser1@192.168.1.67
```

```bash
ssh myuser2@server2
```

---

# 26. Connect Using a Hostname

If DNS or `/etc/hosts` name resolution is configured, use:

```bash
ssh myuser1@server2
```

Test name resolution:

```bash
getent hosts server2
```

Example Output:

```text
192.168.1.67 server2
```

---

# Configure `/etc/hosts` for a Lab

On Server 1:

```bash
sudo vim /etc/hosts
```

Add:

```text
192.168.1.66 server1
192.168.1.67 server2
```

Now test:

```bash
ping -c 2 server2
```

Connect:

```bash
ssh myuser1@server2
```

---

# 27. Use a Custom SSH Port

If the SSH server uses a non-default port, use uppercase `-p`.

Example:

```bash
ssh -p 2222 myuser1@192.168.1.67
```

> `-p` must be lowercase for the port option in the `ssh` command.

---

# 28. Run a Remote Command Without Opening a Full Session

You can execute a command remotely:

```bash
ssh myuser1@192.168.1.67 hostname
```

Example Output:

```text
server2
```

Another example:

```bash
ssh myuser1@192.168.1.67 'whoami && pwd'
```

This connects, executes the commands, displays the output, and then disconnects.

---

# 29. Common Verification Commands

| Command | Purpose |
|---------|---------|
| `whoami` | Show the current username |
| `hostname` | Show the current server name |
| `pwd` | Show the current directory |
| `w` | Show logged-in users and activity |
| `who` | Show logged-in users |
| `id` | Show user and group information |
| `ip a` | Show IP addresses |
| `exit` | End the SSH session |

---

# 30. OpenSSH Client and Server Configuration Files

| File | Purpose |
|------|---------|
| `/etc/ssh/sshd_config` | SSH server configuration |
| `/etc/ssh/ssh_config` | System-wide SSH client configuration |
| `~/.ssh/config` | Per-user SSH client configuration |
| `~/.ssh/known_hosts` | Trusted remote host keys |
| `~/.ssh/authorized_keys` | Public keys allowed to log in |
| `/etc/ssh/ssh_host_*` | Server host keys |

---

# 31. Important SSH Server Commands

Check the service:

```bash
systemctl status sshd
```

Start the service:

```bash
sudo systemctl start sshd
```

Enable it at boot:

```bash
sudo systemctl enable sshd
```

Enable and start immediately:

```bash
sudo systemctl enable --now sshd
```

Restart after configuration changes:

```bash
sudo systemctl restart sshd
```

Check configuration syntax before restarting:

```bash
sudo sshd -t
```

If the command produces no output, the syntax is generally valid.

---

# 32. Important Security Recommendations

To improve SSH security:

- Use SSH keys instead of passwords.
- Disable direct root login when practical.
- Allow only required users.
- Keep OpenSSH updated.
- Use the firewall to restrict access.
- Verify host-key fingerprints.
- Review authentication logs.
- Avoid weak passwords.
- Do not expose SSH unnecessarily to the public internet.
- Consider rate limiting or tools such as Fail2ban when appropriate.

---

# 33. Check SSH Authentication Logs

On Rocky Linux or RHEL:

```bash
sudo tail -f /var/log/secure
```

Using the system journal:

```bash
sudo journalctl -u sshd
```

Follow logs in real time:

```bash
sudo journalctl -u sshd -f
```

These logs can show:

- Successful logins
- Failed passwords
- Invalid users
- Disconnections
- Public-key authentication
- SSH service errors

---

# 34. Basic SSH Connection Flow

```text
User Runs SSH Command
        │
        ▼
Client Contacts Server on Port 22
        │
        ▼
Server Presents Host Key
        │
        ▼
Client Verifies Host Identity
        │
        ▼
User Authentication
        │
        ├── Password
        │
        └── SSH Key
        │
        ▼
Encrypted Session Established
        │
        ▼
User Accesses Remote Shell
```

---

# 🧪 Practice Exercises

## Exercise 1 – Check OpenSSH Packages

```bash
rpm -qa | grep openssh
```

---

## Exercise 2 – Check the SSH Service

```bash
systemctl status sshd
```

---

## Exercise 3 – Create Two Users

```bash
sudo useradd myuser1
sudo useradd myuser2
sudo passwd myuser1
sudo passwd myuser2
```

Perform this on both servers.

---

## Exercise 4 – Check Server IP Addresses

```bash
ip -4 addr
```

---

## Exercise 5 – Connect Using the Same Username

From Server 1:

```bash
su - myuser1
ssh 192.168.1.67
```

---

## Exercise 6 – Verify the Remote System

```bash
whoami
hostname
pwd
w
```

---

## Exercise 7 – Exit the Remote Session

```bash
exit
```

---

## Exercise 8 – Connect as a Different User

```bash
ssh myuser2@192.168.1.67
```

---

## Exercise 9 – Run a Remote Command

```bash
ssh myuser1@192.168.1.67 'hostname && whoami && pwd'
```

---

## Exercise 10 – Review SSH Logs

On Server 2:

```bash
sudo journalctl -u sshd -f
```

Then attempt another SSH login from Server 1 and observe the log entries.

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – Connection Refused

Error:

```text
ssh: connect to host 192.168.1.67 port 22: Connection refused
```

Check on Server 2:

```bash
systemctl status sshd
```

Start the service:

```bash
sudo systemctl enable --now sshd
```

---

### Scenario 2 – Connection Timed Out

Possible causes:

- Wrong IP address
- Firewall blocking port 22
- Network routing problem
- Server is powered off
- SSH is listening on another port

Check:

```bash
ping -c 4 192.168.1.67
```

```bash
nc -zv 192.168.1.67 22
```

---

### Scenario 3 – Permission Denied

Error:

```text
Permission denied, please try again.
```

Possible causes:

- Wrong username
- Wrong password
- User does not exist on the remote server
- Password authentication is disabled
- Account is locked
- SSH configuration denies the user

Check:

```bash
id myuser1
```

```bash
sudo passwd -S myuser1
```

```bash
sudo journalctl -u sshd -n 50
```

---

### Scenario 4 – Hostname Does Not Resolve

Error:

```text
ssh: Could not resolve hostname server2
```

Use the IP address:

```bash
ssh myuser1@192.168.1.67
```

Or fix DNS or `/etc/hosts`.

---

### Scenario 5 – Host Key Changed

Error:

```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

Do not immediately remove the saved key.

First verify why the host key changed.

After confirming that the change is legitimate:

```bash
ssh-keygen -R 192.168.1.67
```

Then reconnect and verify the new fingerprint.

---

### Scenario 6 – Firewall Is Blocking SSH

Check:

```bash
sudo firewall-cmd --list-services
```

Allow SSH:

```bash
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload
```

---

### Scenario 7 – SSH Uses a Different Port

Connect using:

```bash
ssh -p PORT username@server
```

Example:

```bash
ssh -p 2222 myuser1@192.168.1.67
```

---

# 📌 Quick Revision

| Item | Meaning |
|------|---------|
| OpenSSH | Open-source collection of secure networking tools |
| SSH | Secure Shell protocol |
| Default port | `22` |
| `ssh server` | Connect using the current local username |
| `ssh user@server` | Connect using a specified remote username |
| `sshd` | OpenSSH server daemon |
| `known_hosts` | Stores trusted server host keys |
| `w` | Shows logged-in users |
| `pwd` | Shows current directory |
| `whoami` | Shows current user |
| `exit` | Closes the remote session |

---

# Common Commands

| Command | Purpose |
|---------|---------|
| `ssh IP` | Connect with the current username |
| `ssh user@IP` | Connect as a specific user |
| `ssh -p PORT user@IP` | Connect to a custom SSH port |
| `systemctl status sshd` | Check SSH server status |
| `sshd -t` | Validate SSH server configuration |
| `ip a` | Display IP addresses |
| `hostname` | Display server hostname |
| `w` | Display logged-in users |
| `pwd` | Display current directory |
| `exit` | Leave the remote SSH session |
| `journalctl -u sshd` | View SSH service logs |

---

# 📖 Key Takeaways

- OpenSSH is software that implements the SSH protocol.
- SSH provides secure and encrypted remote communication.
- The default SSH port is `22`.
- SSH is more secure than Telnet and traditional FTP.
- If no username is specified, SSH uses the current local username.
- Use `ssh user@server` to connect as a different remote user.
- SSH host keys help detect unexpected server identity changes.
- `w`, `whoami`, `hostname`, and `pwd` help verify a remote session.
- The SSH server service is called `sshd`.
- Always verify host-key warnings before taking corrective action.

---

# 💡 Remember

> **Think of SSH as a secure, encrypted tunnel between two Linux systems.**
>
> - **OpenSSH** provides the tools.
> - **SSH** provides the secure protocol.
> - **Port 22** is the default entry point.
> - **Host keys** confirm the server's identity.
> - **User authentication** confirms the user's identity.
>
> **Golden Connection Format:**
>
> ```text
> ssh username@remote_server
> ```
>
> Example:
>
> ```bash
> ssh myuser1@192.168.1.67
> ```
>
> **Always use SSH instead of insecure protocols such as Telnet for remote Linux administration.**