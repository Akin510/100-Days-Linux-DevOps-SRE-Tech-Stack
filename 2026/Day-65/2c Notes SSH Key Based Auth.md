# MODULE 08 – SSH Key-Based Authentication
> **Configuring Secure Passwordless SSH Login with Public and Private Keys**

---

# 🎯 Learning Objectives

In this lesson, you will learn:

- What SSH Key-Based Authentication is.
- How passwordless SSH authentication works.
- The difference between a Public Key and a Private Key.
- Where SSH keys are stored.
- Why the Private Key must remain secret.
- How the Public Key is installed on a remote server.
- What the `authorized_keys` file does.
- How the SSH authentication challenge-response process works.
- Why compromised Private Keys are dangerous.
- Important security best practices for SSH keys.

---

# 📖 Introduction

By default, SSH may authenticate a user by asking for a password.

Example:

```bash
ssh user1@serverB
```

The remote server may ask:

```text
user1@serverB's password:
```

However, SSH can also be configured to authenticate a user with cryptographic keys instead of an account password.

This is called:

> **SSH Key-Based Authentication**

It is commonly used for:

- Secure remote administration
- Automation
- Backup jobs
- Configuration management
- DevOps pipelines
- Server-to-server communication

---

# 1. What Is SSH Key-Based Authentication?

SSH Key-Based Authentication allows a user to log in to a remote server using a cryptographic key pair.

The key pair contains:

1. **Private Key**
2. **Public Key**

The Private Key remains on the client system.

The Public Key is copied to the remote server.

---

# Key-Based Authentication Layout

```text
Server A – SSH Client
User: user1
│
├── Private Key
│   └── Must remain secret
│
└── Public Key
    └── Copied to Server B
```

```text
Server B – SSH Server
User: user1
│
└── ~/.ssh/authorized_keys
    └── Contains user1's Public Key
```

---

# 2. Practice Lab Environment

Suppose there are two Linux servers:

| Server | Role | User |
|--------|------|------|
| Server A | SSH Client | `user1` |
| Server B | SSH Server | `user1` |

The user `user1` exists on both servers.

The requirement is:

> `user1` on Server A should be able to log in to Server B without entering the remote account password.

---

# 3. Password Authentication

Without SSH keys, the normal login process is:

```bash
ssh user1@serverB
```

Then Server B asks for:

```text
user1@serverB's password:
```

The user must enter the password configured for `user1` on Server B.

---

# Password Authentication Flow

```text
User Runs SSH Command
        │
        ▼
Server Requests Password
        │
        ▼
User Enters Password
        │
        ▼
Server Verifies Password
        │
        ▼
Login Allowed or Denied
```

---

# 4. Key-Based Authentication

With Key-Based Authentication:

- Server A stores the Private Key.
- Server B stores the matching Public Key.
- The user does not need to send the remote account password.
- The server verifies that the client possesses the matching Private Key.

---

# Key-Based Authentication Flow

```text
Server A
Private Key
    │
    │ SSH Connection Request
    ▼
Server B
Public Key in authorized_keys
    │
    │ Cryptographic Verification
    ▼
Identity Confirmed
    │
    ▼
SSH Login Allowed
```

---

# 5. Public Key and Private Key

## Private Key

The Private Key:

- Must remain on the client.
- Must be protected.
- Must never be shared.
- Proves the user's identity.
- May be protected with a passphrase.

Example files:

```text
~/.ssh/id_ed25519
```

or:

```text
~/.ssh/id_rsa
```

---

## Public Key

The Public Key:

- Can be copied to remote servers.
- Is not secret.
- Is placed in the remote user's `authorized_keys` file.
- Works only with the matching Private Key.

Example files:

```text
~/.ssh/id_ed25519.pub
```

or:

```text
~/.ssh/id_rsa.pub
```

---

# Important Correction About Encryption

It is common to say:

- Public Key encrypts.
- Private Key decrypts.

However, SSH user authentication is more accurately described as a cryptographic proof.

The SSH server does not simply send a normal secret message for the client to decrypt.

Instead:

1. The client offers a Public Key.
2. The server checks whether that Public Key is authorized.
3. The client proves possession of the matching Private Key by signing authentication data.
4. The server verifies the signature using the Public Key.
5. The Private Key is never sent over the network.

---

# 6. Where SSH Keys Are Stored

On Server A, while logged in as `user1`, keys are normally stored in:

```text
/home/user1/.ssh/
```

Shortcut:

```text
~/.ssh/
```

Typical files:

```text
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

---

# 7. The `authorized_keys` File

On Server B, the Public Key is stored in:

```text
/home/user1/.ssh/authorized_keys
```

Shortcut:

```text
~/.ssh/authorized_keys
```

The file contains Public Keys that are authorized to log in as that user.

---

# Important Rule

The Public Key must be installed under the correct remote account.

For example, to log in as:

```text
user1
```

the key must be stored in:

```text
/home/user1/.ssh/authorized_keys
```

It should not be placed in another user's home directory.

---

# 8. Generate an SSH Key Pair

Log in to Server A as `user1`.

Verify:

```bash
whoami
```

Expected Output:

```text
user1
```

Generate an ED25519 key pair:

```bash
ssh-keygen -t ed25519
```

---

# Why Use ED25519?

ED25519 is commonly recommended because it provides:

- Strong security
- Small key size
- Fast authentication
- Good performance
- Modern cryptography

---

# Key-Generation Prompt

You may see:

```text
Enter file in which to save the key (/home/user1/.ssh/id_ed25519):
```

Press **Enter** to accept the default path.

Then:

```text
Enter passphrase (empty for no passphrase):
```

You may:

- Enter a strong passphrase for better security.
- Leave it empty for fully unattended automation.

---

# Security Recommendation

For interactive administrator accounts, use a passphrase.

For automation, carefully consider:

- A dedicated service account
- Restricted key permissions
- Command restrictions
- Source-address restrictions
- Secure secret storage

---

# 9. Files Created by `ssh-keygen`

After generation, verify:

```bash
ls -la ~/.ssh
```

Expected files:

```text
id_ed25519
id_ed25519.pub
```

---

# Key File Meaning

| File | Purpose |
|------|---------|
| `id_ed25519` | Private Key |
| `id_ed25519.pub` | Public Key |

---

# 10. Protect the Private Key

Check permissions:

```bash
ls -l ~/.ssh/id_ed25519
```

Typical secure permissions:

```text
-rw-------.
```

Set them if necessary:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Secure the SSH directory:

```bash
chmod 700 ~/.ssh
```

---

# ⚠️ Never Share the Private Key

Do not:

- Email the Private Key.
- Copy it to random servers.
- Store it in public repositories.
- Paste it into tickets or chat.
- Give it to another user.
- Put it in a shared folder.

Anyone who obtains the Private Key may be able to access systems where the matching Public Key is authorized.

---

# 11. View the Public Key

Run:

```bash
cat ~/.ssh/id_ed25519.pub
```

Example:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... user1@serverA
```

The Public Key can safely be copied to the remote server.

---

# 12. Copy the Public Key with `ssh-copy-id`

The recommended method is:

```bash
ssh-copy-id user1@serverB
```

Using an IP address:

```bash
ssh-copy-id user1@192.168.1.67
```

The remote server asks for the account password one final time.

After successful installation, the Public Key is added to:

```text
/home/user1/.ssh/authorized_keys
```

---

# Specify a Particular Public Key

Use:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user1@serverB
```

---

# 13. Manual Public-Key Installation

If `ssh-copy-id` is unavailable, use:

```bash
cat ~/.ssh/id_ed25519.pub | ssh user1@serverB 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys'
```

This command:

1. Reads the local Public Key.
2. Connects to Server B.
3. Creates the remote `.ssh` directory if required.
4. Appends the Public Key to `authorized_keys`.

---

# 14. Verify the Remote Public Key

Log in to Server B:

```bash
ssh user1@serverB
```

Then check:

```bash
cat ~/.ssh/authorized_keys
```

You should see the Public Key copied from Server A.

---

# 15. Correct Remote Permissions

On Server B:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Verify ownership:

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```

The files should belong to:

```text
user1:user1
```

Fix ownership if required:

```bash
chown -R user1:user1 /home/user1/.ssh
```

---

# 16. Test Passwordless Login

From Server A:

```bash
ssh user1@serverB
```

If everything is configured correctly:

- The remote account password is not requested.
- The user logs in as `user1`.
- The remote shell opens.

Verify:

```bash
whoami
hostname
pwd
```

---

# Passwordless Authentication Result

```text
Server A
user1
   │
   │ ssh user1@serverB
   ▼
Server B
user1
   │
   └── No remote account password requested
```

---

# 17. What Happens Behind the Scenes?

A simplified process is:

1. `user1` starts an SSH connection from Server A.
2. Server B presents its Host Key.
3. Server A verifies the identity of Server B.
4. Server A offers the user's Public Key identity.
5. Server B checks `~/.ssh/authorized_keys`.
6. If the key is authorized, Server B asks the client to prove possession of the matching Private Key.
7. Server A signs authentication data using the Private Key.
8. Server B verifies the signature using the Public Key.
9. Authentication succeeds.
10. The encrypted SSH session opens.

---

# Authentication Flow

```text
Client Offers Public Key
        │
        ▼
Server Checks authorized_keys
        │
        ├── Key Not Found ──► Authentication Rejected
        │
        └── Key Found
                │
                ▼
Client Proves Private-Key Possession
                │
                ▼
Server Verifies Signature
                │
                ▼
Authentication Successful
```

---

# 18. The Private Key Never Leaves the Client

A very important security principle is:

> **The Private Key is never copied to the SSH server during normal authentication.**

The server stores only the Public Key.

The client uses the Private Key locally to prove identity.

---

# 19. One Key Can Be Authorized on Multiple Servers

The same Public Key may be copied to:

```text
Server B
Server C
Server D
```

This allows the matching Private Key to authenticate to all those servers.

Example:

```bash
ssh-copy-id user1@serverB
ssh-copy-id user1@serverC
ssh-copy-id user1@serverD
```

---

# Security Impact

If the Private Key is compromised, an attacker may be able to access every server where the matching Public Key is authorized.

This is why the Private Key must remain secure.

---

# 20. Private-Key Compromise

Suppose an attacker obtains:

```text
~/.ssh/id_ed25519
```

The attacker may attempt to log in to all systems where the matching Public Key exists in:

```text
~/.ssh/authorized_keys
```

Possible affected servers:

```text
Server B
Server C
Server D
```

---

# What to Do If a Private Key Is Compromised

Immediately:

1. Remove the matching Public Key from every remote `authorized_keys` file.
2. Generate a new key pair.
3. Copy the new Public Key to approved servers.
4. Investigate how the key was exposed.
5. Review SSH authentication logs.
6. Rotate other credentials if necessary.

---

# 21. Identify a Public-Key Fingerprint

Display the local Public-Key fingerprint:

```bash
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

Example:

```text
256 SHA256:xxxxxxxxxxxxxxxx user1@serverA (ED25519)
```

Fingerprints help identify keys without displaying the full key.

---

# 22. Test Which Key SSH Is Using

Use verbose mode:

```bash
ssh -v user1@serverB
```

For more detail:

```bash
ssh -vv user1@serverB
```

Look for messages such as:

```text
Offering public key
Server accepts key
Authentication succeeded
```

---

# 23. Use a Specific Private Key

If multiple keys exist, specify one with:

```bash
ssh -i ~/.ssh/id_ed25519 user1@serverB
```

Example:

```bash
ssh -i ~/.ssh/project_key user1@192.168.1.67
```

---

# 24. Configure the SSH Client

Edit:

```bash
vim ~/.ssh/config
```

Add:

```text
Host serverB
    HostName 192.168.1.67
    User user1
    IdentityFile ~/.ssh/id_ed25519
```

Secure the file:

```bash
chmod 600 ~/.ssh/config
```

Now connect with:

```bash
ssh serverB
```

---

# 25. Key Passphrase vs Account Password

These are different.

| Credential | Purpose |
|------------|---------|
| Remote account password | Authenticates to the remote operating-system account |
| Private-Key passphrase | Protects the local Private Key file |

If a key has a passphrase, SSH may ask:

```text
Enter passphrase for key '/home/user1/.ssh/id_ed25519':
```

This is not the remote user's account password.

---

# 26. Use `ssh-agent`

`ssh-agent` can hold a decrypted Private Key temporarily in memory.

Start an agent:

```bash
eval "$(ssh-agent -s)"
```

Add the key:

```bash
ssh-add ~/.ssh/id_ed25519
```

List loaded keys:

```bash
ssh-add -l
```

After the key is added, SSH can use it without repeatedly asking for the key passphrase during that session.

---

# 27. SSH Server Configuration

The SSH server configuration file is:

```text
/etc/ssh/sshd_config
```

Important settings include:

```text
PubkeyAuthentication yes
```

On many systems, this is enabled by default.

Check the effective configuration:

```bash
sudo sshd -T | grep -i pubkeyauthentication
```

Expected Output:

```text
pubkeyauthentication yes
```

---

# 28. Validate SSH Server Configuration

Before restarting `sshd`, run:

```bash
sudo sshd -t
```

If no output appears, the syntax is generally valid.

Then restart if needed:

```bash
sudo systemctl restart sshd
```

---

# 29. Disabling Password Authentication

After confirming that Key-Based Authentication works, password authentication may be disabled for stronger security.

Edit:

```bash
sudo vim /etc/ssh/sshd_config
```

Set:

```text
PasswordAuthentication no
```

Validate:

```bash
sudo sshd -t
```

Restart:

```bash
sudo systemctl restart sshd
```

---

# ⚠️ Avoid Locking Yourself Out

Before disabling password authentication:

- Keep the current SSH session open.
- Open a second terminal.
- Test Key-Based Authentication.
- Confirm `sudo` or administrative access.
- Verify the correct key is being used.
- Confirm firewall and SSH service status.

Do not close the working session until the new login has been tested successfully.

---

# 30. Root Login Security

A commonly recommended server setting is:

```text
PermitRootLogin prohibit-password
```

This disables root password login while potentially allowing root Key-Based Authentication.

A stricter option is:

```text
PermitRootLogin no
```

For most environments, administrators should:

1. Log in as a normal user.
2. Use `sudo` for administrative tasks.

---

# 31. Remove an Authorized Key

On Server B, edit:

```bash
vim ~/.ssh/authorized_keys
```

Delete the line containing the unwanted Public Key.

A safer approach is to identify the key by its comment or fingerprint.

After removal, the matching Private Key can no longer authenticate as that user.

---

# 32. Add Comments to Keys

A key comment helps identify its owner or purpose.

Generate a key with a custom comment:

```bash
ssh-keygen -t ed25519 -C "user1@serverA"
```

For automation:

```bash
ssh-keygen -t ed25519 -C "backup-service-serverA"
```

The comment does not provide security by itself, but it improves key management.

---

# 33. Restrict an Authorized Key

An entry in `authorized_keys` can include restrictions.

Example:

```text
from="192.168.1.66",no-agent-forwarding,no-port-forwarding,no-pty ssh-ed25519 AAAAC3... user1@serverA
```

Possible restrictions include:

| Restriction | Purpose |
|-------------|---------|
| `from="IP"` | Allow the key only from a specific source |
| `no-agent-forwarding` | Disable SSH agent forwarding |
| `no-port-forwarding` | Disable port forwarding |
| `no-pty` | Do not allocate an interactive terminal |
| `command="..."` | Force a specific command |

These options are especially useful for automated service accounts.

---

# 34. Common SSH Key Files

| File | Purpose |
|------|---------|
| `~/.ssh/id_ed25519` | ED25519 Private Key |
| `~/.ssh/id_ed25519.pub` | ED25519 Public Key |
| `~/.ssh/id_rsa` | RSA Private Key |
| `~/.ssh/id_rsa.pub` | RSA Public Key |
| `~/.ssh/authorized_keys` | Public Keys allowed to log in |
| `~/.ssh/config` | Per-user SSH client configuration |
| `~/.ssh/known_hosts` | Trusted SSH server Host Keys |

---

# 35. Host Keys vs User Keys

Do not confuse these two key systems.

| Feature | Host Keys | User Keys |
|---------|-----------|-----------|
| Purpose | Authenticate server | Authenticate user |
| Private Key Location | `/etc/ssh/ssh_host_*` on server | `~/.ssh/id_*` on client |
| Public Key Location | Presented by server and stored in `known_hosts` | Stored in remote `authorized_keys` |
| Verification Direction | Server to client | User to server |

---

# 36. Complete SSH Security Flow

```text
Step 1: Client Verifies Server
        │
        └── Server Host Key checked against known_hosts
                       │
                       ▼
Step 2: Server Verifies User
        │
        └── User Public Key checked in authorized_keys
                       │
                       ▼
Step 3: Client Proves Private-Key Possession
                       │
                       ▼
Step 4: Encrypted SSH Session Opens
```

---

# 🧪 Practice Lab

## Step 1 – Verify the User on Server A

```bash
whoami
```

Expected:

```text
user1
```

---

## Step 2 – Generate the Key Pair

```bash
ssh-keygen -t ed25519
```

Accept the default location.

---

## Step 3 – Verify the Files

```bash
ls -la ~/.ssh
```

---

## Step 4 – View the Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

---

## Step 5 – Copy the Public Key

```bash
ssh-copy-id user1@serverB
```

---

## Step 6 – Verify Remote Permissions

On Server B:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## Step 7 – Test Key-Based Login

From Server A:

```bash
ssh user1@serverB
```

---

## Step 8 – Verify Identity

```bash
whoami
hostname
pwd
```

---

## Step 9 – Test with Verbose Output

```bash
ssh -v user1@serverB
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – SSH Still Asks for the Remote Password

Possible causes:

- Public Key was copied to the wrong user.
- Incorrect file permissions.
- Incorrect ownership.
- `PubkeyAuthentication` is disabled.
- Client is using the wrong Private Key.
- Public Key is missing from `authorized_keys`.
- SELinux context is incorrect.

Check:

```bash
ssh -vv user1@serverB
```

---

### Scenario 2 – Fix Remote Permissions

On Server B:

```bash
chmod 700 /home/user1/.ssh
chmod 600 /home/user1/.ssh/authorized_keys
chown -R user1:user1 /home/user1/.ssh
```

---

### Scenario 3 – Restore SELinux Context

On Rocky Linux or RHEL:

```bash
sudo restorecon -Rv /home/user1/.ssh
```

---

### Scenario 4 – Wrong Private Key Is Used

Specify the key:

```bash
ssh -i ~/.ssh/id_ed25519 user1@serverB
```

Use verbose mode:

```bash
ssh -vv -i ~/.ssh/id_ed25519 user1@serverB
```

---

### Scenario 5 – Public-Key Authentication Is Disabled

Check:

```bash
sudo sshd -T | grep -i pubkeyauthentication
```

If required, configure:

```text
PubkeyAuthentication yes
```

Then validate:

```bash
sudo sshd -t
```

Restart:

```bash
sudo systemctl restart sshd
```

---

### Scenario 6 – `ssh-copy-id` Is Not Available

Install the OpenSSH client tools:

```bash
sudo dnf install -y openssh-clients
```

Or use the manual copy command:

```bash
cat ~/.ssh/id_ed25519.pub | ssh user1@serverB 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys'
```

---

### Scenario 7 – The Private Key Has Been Compromised

On every affected server:

1. Edit:

   ```bash
   vim ~/.ssh/authorized_keys
   ```

2. Remove the compromised Public Key.

3. Generate a new pair:

   ```bash
   ssh-keygen -t ed25519
   ```

4. Copy the new Public Key.

5. Review SSH logs:

   ```bash
   sudo journalctl -u sshd
   ```

---

# ⚠️ Security Best Practices

- Use ED25519 keys when supported.
- Protect Private Keys with passphrases.
- Never share a Private Key.
- Use a separate key for important environments.
- Use dedicated service accounts for automation.
- Restrict automation keys in `authorized_keys`.
- Remove unused Public Keys.
- Review `authorized_keys` regularly.
- Keep OpenSSH updated.
- Disable password authentication only after testing Key-Based Authentication.
- Use `ssh-agent` carefully.
- Avoid copying one administrator's Private Key to multiple users or devices.

---

# 📌 Quick Revision

| Item | Purpose |
|------|---------|
| Private Key | Remains secret on the client |
| Public Key | Copied to remote server |
| `ssh-keygen` | Generate a key pair |
| `ssh-copy-id` | Install the Public Key remotely |
| `authorized_keys` | Stores allowed Public Keys |
| `id_ed25519` | ED25519 Private Key |
| `id_ed25519.pub` | ED25519 Public Key |
| `ssh -i KEY` | Use a specific Private Key |
| `ssh-agent` | Temporarily stores unlocked keys |
| `PubkeyAuthentication` | Controls server Public-Key authentication |

---

# Common Commands

| Command | Purpose |
|---------|---------|
| `ssh-keygen -t ed25519` | Generate an ED25519 key pair |
| `ssh-copy-id user@server` | Copy the Public Key |
| `cat ~/.ssh/id_ed25519.pub` | View the Public Key |
| `ssh user@server` | Test Key-Based Authentication |
| `ssh -i KEY user@server` | Use a specific Private Key |
| `ssh -v user@server` | Debug SSH authentication |
| `ssh-add KEY` | Add a key to `ssh-agent` |
| `ssh-add -l` | List loaded agent keys |
| `sshd -T` | Show effective SSH server configuration |
| `restorecon -Rv ~/.ssh` | Restore SELinux contexts |

---

# 📖 Key Takeaways

- SSH Key-Based Authentication uses a Public and Private Key pair.
- The Private Key remains on the client and must stay secret.
- The Public Key is copied to the remote user's `authorized_keys` file.
- SSH verifies that the client possesses the matching Private Key.
- The Private Key is never sent to the server.
- The same Public Key may be authorized on multiple servers.
- Compromise of the Private Key may expose every server that trusts it.
- Use `ssh-copy-id` to install keys safely.
- Correct ownership, permissions, and SELinux contexts are essential.
- Test Key-Based Authentication before disabling password login.

---

# 💡 Remember

> **Think of SSH Key-Based Authentication as a lock-and-proof system.**
>
> - The **Public Key** is installed on the server.
> - The **Private Key** stays with the user.
> - The server checks whether the user can prove possession of the matching Private Key.
> - The Private Key itself is never sent across the network.
>
> **Golden Rule:**
>
> ```text
> Private Key = Keep Secret
>
> Public Key = Copy to authorized_keys
> ```
>
> **Anyone who obtains the Private Key may be able to access every server that trusts its matching Public Key.**