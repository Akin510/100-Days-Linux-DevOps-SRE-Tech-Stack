# Comprehensive Lesson Plan: Core SSH Concepts & Windows Client-Server Handshake

Use this guide to teach students how the Secure Shell (SSH) protocol validates identities, establishes trust, manages SSH configuration directories on Windows, and securely authenticates remote connections.

---

# 🎯 Learning Objectives

By the end of this lesson, students will be able to:

- Understand how SSH establishes a secure connection.
- Explain the two major SSH security verification stages.
- Identify the difference between **Server Authentication** and **User Authentication**.
- Understand the purpose of the `known_hosts` file.
- Understand the purpose of the `.ssh` directory.
- Locate SSH configuration files on Windows.
- Explain how SSH prevents Man-in-the-Middle (MITM) attacks.
- Understand Trust On First Use (TOFU).
- Perform Out-of-Band Host Key Verification.

---

# 📖 Introduction

Secure Shell (**SSH**) is the standard protocol used for securely accessing remote Linux systems.

SSH provides three important security services:

- Encryption
- Authentication
- Data Integrity

SSH ensures that:

- The client knows it is communicating with the correct server.
- The server verifies the identity of the user attempting to log in.
- All communication between the client and server is encrypted.

SSH performs **two separate security verification steps** before granting access.

---

# SSH Authentication Flow

```text
            [ SSH Client ]                                   [ SSH Server ]
                  |                                                  |
                  |------------- 1. Initiate Connection ------------->|
                  |                                                  |
                  |<------------ 2. Send Host Key --------------------|
                  |                                                  |
                  |==================================================|
                  |              SECURITY CHECK #1                   |
                  |                                                  |
                  | Client checks the local known_hosts database.    |
                  |                                                  |
                  | Does the Host Key match?                         |
                  |                                                  |
                  | YES → Server identity VERIFIED                   |
                  | NO  → Warning or Connection Blocked              |
                  |==================================================|
                  |                                                  |
                  |======== Secure Encrypted SSH Tunnel =============>|
                  |                                                  |
                  |------------- 3. Send Credentials ---------------->|
                  |        (Password or Private Key Signature)        |
                  |                                                  |
                  |                                ================================|
                  |                                SECURITY CHECK #2              |
                  |                                                  |
                  |      Server checks the supplied credentials       |
                  |      against:                                    |
                  |                                                  |
                  |      • /etc/shadow                               |
                  |      • ~/.ssh/authorized_keys                    |
                  |                                                  |
                  |      User identity VERIFIED                      |
                  |                                ================================|
                  |                                                  |
                  |<------------ 4. Shell Access Granted ------------|
```

---

# Step-by-Step SSH Authentication Process

## Step 1 – Client Initiates a Connection

The SSH client initiates a connection to the remote SSH server.

Example:

```bash
ssh student@192.168.1.11
```

By default, SSH connects using:

```text
TCP Port 22
```

---

## Step 2 – Server Sends Its Host Key

Before asking for your password, the SSH server sends its **Public Host Key** to the client.

This Host Key uniquely identifies the SSH server.

It is **not** your user authentication key.

It represents the server's identity.

---

# Security Check #1 – Server Verification

The SSH client now verifies whether the server can be trusted.

It does this by comparing the received Host Key with the one stored locally inside the:

```text
known_hosts
```

file.

If the keys match:

```text
✓ Server identity verified
```

If the keys do not match:

```text
WARNING:
REMOTE HOST IDENTIFICATION HAS CHANGED!
```

This warning helps protect against:

- Man-in-the-Middle (MITM) attacks
- DNS spoofing
- IP hijacking
- Host impersonation

---

# Step 3 – Secure Encrypted Tunnel

After verifying the server's identity, SSH negotiates encryption keys.

Only after encryption is established are credentials exchanged.

Everything transmitted after this point is encrypted.

This includes:

- Passwords
- Commands
- Terminal output
- Files transferred with SCP or SFTP

---

# Step 4 – User Authentication

Once the encrypted tunnel has been established, the client sends user credentials.

These credentials can be:

- Password Authentication
- SSH Private Key Authentication

The server validates the user's identity.

---

# Security Check #2 – User Verification

The server checks the supplied credentials against its local authentication database.

Depending on the authentication method, it checks:

For password authentication:

```text
/etc/shadow
```

For SSH key authentication:

```text
~/.ssh/authorized_keys
```

If authentication succeeds:

```text
✓ User authenticated
```

---

# Step 5 – Access Granted

Once both security checks succeed:

- Server Identity Verified
- User Identity Verified

the SSH server grants access and starts the user's shell session.

Example:

```bash
[root@server ~]#
```

or

```bash
[student@server ~]$
```

---

# Why SSH Uses Two Separate Verification Steps

Many beginners assume SSH performs only one authentication step.

In reality, SSH performs two completely different checks.

| Verification | Verified By | Purpose |
|--------------|-------------|---------|
| Server Authentication | Client | Ensures the client is communicating with the correct server |
| User Authentication | Server | Ensures the user is authorized to log in |

This layered security model greatly improves overall security.

---

# Windows SSH Directory Structure

When students execute:

```cmd
ssh root@192.168.1.11
```

from **Windows Command Prompt** or **PowerShell**, OpenSSH stores all SSH-related files inside the user's `.ssh` directory.

It is important to distinguish between:

- The directory (folder)
- The individual files stored inside it

---

# The `.ssh` Directory

The `.ssh` folder acts like a filing cabinet.

It stores:

- SSH keys
- Configuration files
- Trusted server database
- Historical host key backups

---

# Windows `.ssh` Directory Layout

```text
C:\Users\<Username>\.ssh\
│
├── config
├── id_rsa
├── id_rsa.pub
├── known_hosts
└── known_hosts.old
```

---

# Purpose of Each File

| File | Purpose |
|------|---------|
| `config` | Stores custom SSH client settings and shortcuts |
| `id_rsa` | User's private key (must remain secret) |
| `id_rsa.pub` | User's public key (safe to share) |
| `known_hosts` | Stores trusted SSH server host keys |
| `known_hosts.old` | Backup of previous host key database |

---

# Viewing SSH Files from Windows Command Prompt

Display the entire `known_hosts` file:

```cmd
type %USERPROFILE%\.ssh\known_hosts
```

---

Display the file one page at a time:

```cmd
more %USERPROFILE%\.ssh\known_hosts
```

Press the **Space Bar** to move to the next page.

---

Open the file using Notepad:

```cmd
notepad %USERPROFILE%\.ssh\known_hosts
```

---

# Understanding the `known_hosts` File

The `known_hosts` file acts as the client's database of trusted SSH servers.

Each time you successfully connect to a new SSH server, its public Host Key is stored in this file.

Future connections compare the received Host Key with the stored value.

If they match:

```text
Connection continues securely.
```

If they do not match:

```text
WARNING:
REMOTE HOST IDENTIFICATION HAS CHANGED!
```

---

# Classroom Security Question

Students often ask:

> "If the server openly sends its Host Key, isn't that insecure?"

The answer is:

**No.**

The server sends its **Public Host Key**, not its private key.

A public key is designed to be shared.

Think of it as:

- A company ID badge
- A business card
- A passport photo

Anyone may see it, but it cannot be used to impersonate the server.

---

# Trust On First Use (TOFU)

The only time SSH cannot verify the server is during the very first connection.

This is known as:

```text
Trust On First Use (TOFU)
```

Because the client has never seen this server before, it has nothing to compare the Host Key against.

SSH displays a message similar to:

```text
The authenticity of host '192.168.1.11' can't be established.

Are you sure you want to continue connecting (yes/no)?
```

If the user blindly types:

```text
yes
```

the Host Key is stored in the `known_hosts` file.

Future connections will compare against this stored value.

---

# The Risk of TOFU

The danger is not that the Host Key is public.

The danger is that during the very first connection, the client cannot be certain the key truly belongs to the intended server.

An attacker performing a Man-in-the-Middle attack could present a fraudulent Host Key.

If the user blindly accepts it, the attacker may be trusted instead of the real server.

---

# Best Practice – Out-of-Band Verification

The safest approach is to verify the server's fingerprint using an independent communication method.

This process is called:

> **Out-of-Band Verification**

---

# Classroom Demonstration

## Step 1 – Display the Server Fingerprint

On the Linux server, execute:

```bash
ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

Example Output:

```text
256 SHA256:abc123xyz456... root@server (ED25519)
```

---

## Step 2 – Publish the Fingerprint

Provide the fingerprint to students through a trusted channel, such as:

- Whiteboard
- Microsoft Teams
- Zoom Chat
- Slack
- Classroom projector

---

## Step 3 – Student Verification

When students connect for the first time, they should compare the displayed fingerprint with the one provided by the instructor.

If they match:

```text
yes
```

can safely be entered.

The Host Key is then permanently stored inside:

```text
known_hosts
```

---

# Security Summary

```text
First Connection
       │
       ▼
Server Sends Public Host Key
       │
       ▼
Student Verifies Fingerprint
       │
       ▼
Accept Key
       │
       ▼
Host Key Saved to known_hosts
       │
       ▼
Future Connections Automatically Verified
```

---

# 📌 Quick Revision

| Component | Purpose |
|------------|---------|
| Host Key | Identifies the SSH server |
| Private Key | Authenticates the user |
| Public Key | Shared openly for verification |
| known_hosts | Client database of trusted servers |
| authorized_keys | Server database of trusted users |
| TOFU | Trust On First Use |
| Out-of-Band Verification | Independent fingerprint verification |

---

# 📖 Key Takeaways

- SSH performs **two independent security checks**.
- The first check verifies the server.
- The second check verifies the user.
- Host Keys identify servers.
- User keys identify users.
- The `known_hosts` file stores trusted server identities.
- The `authorized_keys` file stores trusted user public keys.
- The first connection requires Trust On First Use (TOFU).
- Out-of-Band Verification eliminates blind trust and significantly improves security.

---

# 💡 Remember

> **Think of SSH as entering a secure office building.**
>
> - First, you verify the building's identity by checking its address and security badge (**Server Authentication**).
> - Next, the building verifies your identity using your employee ID or access card (**User Authentication**).
> - Only after both checks succeed are you allowed inside.
>
> **Golden Rule:**
>
> ```text
> Verify Server
>       │
>       ▼
> Establish Encryption
>       │
>       ▼
> Verify User
>       │
>       ▼
> Access Granted
> ```