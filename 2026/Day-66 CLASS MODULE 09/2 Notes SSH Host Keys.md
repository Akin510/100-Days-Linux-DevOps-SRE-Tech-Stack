# MODULE 08 – SSH Host Keys
> **Understanding SSH Server Authentication, Host-Key Verification, and `known_hosts`**

---

# 🎯 Learning Objectives

In this lesson, you will learn:

- What SSH Host Keys are.
- How an SSH client verifies the identity of an SSH server.
- The difference between Host Keys and User Authentication Keys.
- What happens during the first SSH connection.
- Where server Host Keys are stored.
- Where the client stores trusted Host Keys.
- How the `known_hosts` file works.
- Why SSH displays a Host-Key warning.
- How Host Keys help protect against Man-in-the-Middle attacks.
- How to configure strict Host-Key checking safely.

---

# 📖 Introduction
---
## SSH Authentication Flow Diagram

```text
  [ SSH Client ]                                         [ SSH Server ]
        |                                                      |
        | ------------ 1. Initiates Connection --------------> |
        |                                                      |
        | <----------- 2. Sends Server Host Key -------------- |
        |                                                      |
  ============= SECURITY CHECK #1 =============                |
  | Client checks local 'known_hosts' file.   |                |
  | Matches? Server identity is VERIFIED.     |                |
  =============================================                |
        |                                                      |
        |                                                      |
        | === 3. Establish Secure Encrypted Tunnel (Session) ==|
        |======================================================|
        |                                                      |
        |                                                      |
        | ---------- 4. Sends User Credentials --------------> |
        |            (Password or Client Private Key)          |
        |                                                      |
        |                                        ============= SECURITY CHECK #2 =============
        |                                        | Server checks 'authorized_keys' or passwd.|
        |                                        | Matches? User identity is VERIFIED.       |
        |                                        =============================================
        |                                                      |
        | <----------- 5. Access Granted (Shell) ------------- |
        v                                                      v
---
SSH provides secure and encrypted communication between systems.

When an SSH client connects to an SSH server, two important security checks take place:

1. The client verifies the identity of the server.
2. The server verifies the identity of the user.

SSH **Host Keys** are used for the first task:

> **Authenticating the SSH server to the client.**

This helps the client confirm that it is connecting to the correct server and not to an attacker pretending to be that server.

---

# Important Terminology

The correct term is:

```text
SSH Host Keys
```

Not:

```text
SSH Hot Keys
```

A Host Key identifies an SSH server.

---

# 1. What Is an SSH Host Key?

An SSH Host Key is a cryptographic key pair that belongs to the SSH server.

The pair contains:

- A Private Host Key
- A Public Host Key

The private key remains on the SSH server.

The public key is presented to SSH clients during connection setup.

---

# Host-Key Pair

```text
SSH Server
│
├── Private Host Key
│   └── Must remain secret on the server
│
└── Public Host Key
    └── Shared with connecting clients
```

---

# 2. Purpose of SSH Host Keys

SSH Host Keys are used to:

- Identify the SSH server.
- Prove that the server possesses its private key.
- Prevent impersonation of the server.
- Detect unexpected server identity changes.
- Help protect against Man-in-the-Middle attacks.

A Host Key is similar to an identity card for the SSH server.

---

# 3. Host Keys vs User SSH Keys

Host Keys and User SSH Keys are different.

| Key Type | Purpose |
|----------|---------|
| SSH Host Key | Authenticates the server to the client |
| User SSH Key | Authenticates the user to the server |

---

# SSH Authentication Direction

```text
Host Key
Server ─────────────► Client
Proves the server's identity

User Key or Password
Client ─────────────► Server
Proves the user's identity
```

---

# 4. How SSH Encryption Is Established

The server Host Key is important, but it does not directly encrypt the entire SSH session by itself.

A simplified SSH connection works like this:

1. The client contacts the SSH server.
2. Client and server negotiate encryption algorithms.
3. They perform a cryptographic key exchange.
4. Temporary symmetric session keys are created.
5. The server proves its identity using its Host Key.
6. The client verifies the server's Host Key.
7. The user authenticates with a password or SSH key.
8. The encrypted session begins.

---

# Simplified SSH Connection Flow

```text
SSH Client
    │
    │ Connects to TCP Port 22
    ▼
SSH Server
    │
    │ Sends Host-Key Information
    ▼
Client Verifies Server Identity
    │
    │ Key Exchange Creates Session Keys
    ▼
Encrypted Communication Channel
    │
    │ User Authentication
    ▼
Secure Remote Shell
```

---

# 5. Practice Lab Environment

Assume the following lab systems:

| Server | Hostname | Example IP |
|--------|----------|------------|
| Server 1 | `server1` | `192.168.1.66` |
| Server 2 | `server2` | `192.168.1.67` |

A user named:

```text
manish
```

is logged in to Server 1 and wants to connect to Server 2.

---

# 6. Connect to the SSH Server

From Server 1, run:

```bash
ssh manish@192.168.1.67
```

If the same username exists locally and remotely, you may use:

```bash
ssh 192.168.1.67
```

SSH will then use the current local username by default.

---

# 7. First-Time SSH Connection

When connecting to a server for the first time, SSH may display:

```text
The authenticity of host '192.168.1.67 (192.168.1.67)' can't be established.
ED25519 key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxx.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

This message means:

- The client has never trusted this server before.
- The server's Host Key is not yet stored locally.
- SSH is asking whether you trust this server identity.

---

# 8. Verify the Host-Key Fingerprint

Before typing:

```text
yes
```

you should ideally verify the fingerprint through a trusted method.

For example, log in directly to Server 2 and run:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

Example Output:

```text
256 SHA256:xxxxxxxxxxxxxxxxxxxxxxxx root@server2 (ED25519)
```

Compare this fingerprint with the one displayed on Server 1.

If they match, the server identity is confirmed.

---

# Why Fingerprint Verification Matters

If you accept an unverified Host Key, you may accidentally trust:

- The wrong server.
- A reconfigured system.
- A different machine using the same IP.
- A malicious Man-in-the-Middle system.

---

# 9. Accept the Host Key

After confirming the fingerprint, type:

```text
yes
```

You may see:

```text
Warning: Permanently added '192.168.1.67' (ED25519) to the list of known hosts.
```

The Host Key is then stored in the client's:

```text
~/.ssh/known_hosts
```

After this, SSH asks for the remote user's authentication credentials.

Example:

```text
manish@192.168.1.67's password:
```

---

# 10. The Client `known_hosts` File

The client stores trusted server Host Keys in:

```text
~/.ssh/known_hosts
```

For user `manish`, this may be:

```text
/home/manish/.ssh/known_hosts
```

View the file:

```bash
cat ~/.ssh/known_hosts
```

---

# Purpose of `known_hosts`

The `known_hosts` file allows the SSH client to remember:

> **Which Host Key belongs to which remote server.**

On future connections, SSH compares:

- The Host Key currently presented by the server.
- The Host Key already stored in `known_hosts`.

---

# Known-Hosts Comparison

```text
Server Presents Current Host Key
                │
                ▼
Client Reads Saved Host Key
from ~/.ssh/known_hosts
                │
                ▼
Keys Are Compared
        │
        ├── Match ─────► Connection Continues
        │
        └── No Match ──► Security Warning
```

---

# 11. Server Host-Key Files

The SSH server normally stores Host Keys under:

```text
/etc/ssh/
```

Common Host-Key files include:

```text
/etc/ssh/ssh_host_ed25519_key
/etc/ssh/ssh_host_ed25519_key.pub
/etc/ssh/ssh_host_ecdsa_key
/etc/ssh/ssh_host_ecdsa_key.pub
/etc/ssh/ssh_host_rsa_key
/etc/ssh/ssh_host_rsa_key.pub
```

---

# Private vs Public Host-Key Files

| File Ending | Meaning |
|-------------|---------|
| No `.pub` | Private Host Key |
| `.pub` | Public Host Key |

Example:

```text
ssh_host_ed25519_key
```

is the private key.

```text
ssh_host_ed25519_key.pub
```

is the public key.

---

# ⚠️ Important Security Rule

Never share the server's private Host Key.

Private Host-Key files must remain protected on the server.

Check permissions:

```bash
ls -l /etc/ssh/ssh_host_*
```

Private keys are normally readable only by `root`.

---

# 12. View the Server Public Host Key

On Server 2:

```bash
sudo cat /etc/ssh/ssh_host_ed25519_key.pub
```

Example:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... root@server2
```

However, the raw line in `known_hosts` may not look exactly the same because:

- The hostname or IP may be included.
- Hostnames may be hashed.
- The storage format differs.
- Multiple key algorithms may exist.

The safest comparison method is by fingerprint.

---

# 13. Display a Server Host-Key Fingerprint

On Server 2:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

For an ECDSA Host Key:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ecdsa_key.pub
```

For an RSA Host Key:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_rsa_key.pub
```

---

# 14. Future SSH Connections

The next time Manish connects:

```bash
ssh manish@192.168.1.67
```

the SSH client performs this comparison:

```text
Saved Host Key in known_hosts
             VS
Host Key Presented by Server 2
```

If both match:

- The client trusts the server identity.
- No first-time confirmation is required.
- User authentication continues.
- The encrypted SSH session is established.

---

# 15. What If the Host Key Changes?

If the saved Host Key and the server's current Host Key do not match, SSH may display:

```text
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
```

You may also see:

```text
Host key verification failed.
```

---

# Common Reasons for a Changed Host Key

A Host Key may change because:

- The server was reinstalled.
- The operating system was rebuilt.
- SSH Host Keys were regenerated.
- A VM was restored from a different image.
- The IP address now belongs to another server.
- A DNS record points to a different system.
- A server was replaced.
- A Man-in-the-Middle attack is occurring.

---

# ⚠️ Never Ignore the Warning Automatically

A changed Host Key is a security warning.

Do not immediately remove the old key.

First verify:

- Is this the correct server?
- Was the server recently rebuilt?
- Was the IP address reassigned?
- Did an administrator regenerate Host Keys?
- Does the new fingerprint match the real server?

---

# 16. Check the Stored Host Key

Search the local `known_hosts` file:

```bash
ssh-keygen -F 192.168.1.67
```

If connecting by hostname:

```bash
ssh-keygen -F server2
```

---

# 17. Remove an Old Host Key Safely

Only after confirming that the change is legitimate, remove the old entry:

```bash
ssh-keygen -R 192.168.1.67
```

For a hostname:

```bash
ssh-keygen -R server2
```

Then reconnect:

```bash
ssh manish@192.168.1.67
```

Verify the new fingerprint before accepting it.

---

# 18. Do Not Manually Corrupt `known_hosts`

You can technically edit:

```text
~/.ssh/known_hosts
```

with a text editor, but this is not the recommended way to test Host-Key changes.

Use:

```bash
ssh-keygen -R hostname_or_ip
```

to remove entries safely.

Manually changing random characters may produce invalid-file or parsing errors rather than a realistic Host-Key mismatch.

---

# 19. Strict Host-Key Checking

SSH clients support a setting called:

```text
StrictHostKeyChecking
```

This is a **client-side setting**.

It belongs in:

```text
/etc/ssh/ssh_config
```

or:

```text
~/.ssh/config
```

It does **not** belong in:

```text
/etc/ssh/sshd_config
```

because `sshd_config` controls the SSH server, not the SSH client.

---

# 20. `StrictHostKeyChecking` Values

| Value | Behavior |
|-------|----------|
| `yes` | Never automatically add new Host Keys; changed keys are rejected |
| `ask` | Ask before adding a new Host Key; changed keys are rejected |
| `accept-new` | Automatically accept new hosts, but reject changed keys |
| `no` | Automatically accept new keys and may allow some changed-key situations with warnings |

The default behavior is commonly similar to:

```text
ask
```

---

# 21. Configure Strict Checking for One User

Edit:

```bash
vim ~/.ssh/config
```

Add:

```text
Host *
    StrictHostKeyChecking yes
```

Secure the file:

```bash
chmod 600 ~/.ssh/config
```

---

# What Happens with `yes`?

With:

```text
StrictHostKeyChecking yes
```

SSH will:

- Reject unknown Host Keys.
- Reject changed Host Keys.
- Avoid interactive acceptance of untrusted servers.

Example error:

```text
No ED25519 host key is known for 192.168.1.67 and you have requested strict checking.
Host key verification failed.
```

---

# 22. Configure Strict Checking for One Server

A more practical configuration is:

```text
Host server2
    HostName 192.168.1.67
    User manish
    StrictHostKeyChecking yes
```

Now connect using:

```bash
ssh server2
```

---

# 23. System-Wide Client Configuration

For all users, edit:

```bash
sudo vim /etc/ssh/ssh_config
```

Or create a file under:

```text
/etc/ssh/ssh_config.d/
```

Example:

```bash
sudo vim /etc/ssh/ssh_config.d/99-host-key-checking.conf
```

Add:

```text
Host *
    StrictHostKeyChecking yes
```

> Client configuration changes do not normally require restarting `sshd`.

---

# Important Correction

This is not required after changing client settings:

```bash
systemctl restart sshd
```

Restarting `sshd` is only relevant when changing the SSH server configuration, such as:

```text
/etc/ssh/sshd_config
```

For client settings in `~/.ssh/config`, simply start a new SSH connection.

---

# 24. Check Effective SSH Client Configuration

Run:

```bash
ssh -G server2 | grep -i stricthostkeychecking
```

Example Output:

```text
stricthostkeychecking true
```

You can also inspect the Host-Key file setting:

```bash
ssh -G server2 | grep -i userknownhostsfile
```

---

# 25. Preload a Trusted Host Key

In automated environments, you may preload a server Host Key using:

```bash
ssh-keyscan
```

Example:

```bash
ssh-keyscan -H 192.168.1.67 >> ~/.ssh/known_hosts
```

However:

> `ssh-keyscan` retrieves a Host Key, but it does not independently prove that the key is trustworthy.

You must verify the fingerprint through a trusted channel before trusting it.

---

# Safer Preloading Workflow

```bash
ssh-keyscan -t ed25519 192.168.1.67 > /tmp/server2.key
```

Display its fingerprint:

```bash
ssh-keygen -lf /tmp/server2.key
```

Compare it with Server 2:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

If they match, add it:

```bash
cat /tmp/server2.key >> ~/.ssh/known_hosts
```

---

# 26. Hash Hostnames in `known_hosts`

OpenSSH may store hostnames in hashed form.

A hashed entry may look like:

```text
|1|xxxxxxxx|yyyyyyyy ssh-ed25519 AAAAC3...
```

This helps prevent someone who reads the file from easily discovering all the servers you access.

Hash the current file:

```bash
ssh-keygen -H -f ~/.ssh/known_hosts
```

---

# 27. Test Host-Key Verification

## Step 1: Remove the Existing Test Entry

After confirming it is only a lab system:

```bash
ssh-keygen -R 192.168.1.67
```

---

## Step 2: Connect Again

```bash
ssh manish@192.168.1.67
```

You should receive the first-time Host-Key prompt.

---

## Step 3: Verify the Fingerprint

On Server 2:

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

---

## Step 4: Accept the Key

Type:

```text
yes
```

---

## Step 5: Verify the Local Entry

```bash
ssh-keygen -F 192.168.1.67
```

---

# 28. Interview Questions

## Question 1: What Is an SSH Host Key?

**Answer:**

An SSH Host Key is a cryptographic key used to authenticate the SSH server to the client.

---

## Question 2: Where Are Server Host Keys Stored?

**Answer:**

```text
/etc/ssh/ssh_host_*
```

---

## Question 3: Where Does the Client Store Trusted Host Keys?

**Answer:**

```text
~/.ssh/known_hosts
```

---

## Question 4: What Happens During the First SSH Connection?

**Answer:**

The server presents its Host Key, the client displays its fingerprint, and the user decides whether to trust and save it.

---

## Question 5: What Happens on Future Connections?

**Answer:**

The client compares the server's current Host Key with the saved key in `known_hosts`.

---

## Question 6: Why Does SSH Warn About a Changed Host Key?

**Answer:**

The server may have been rebuilt, replaced, reconfigured, or impersonated by an attacker.

---

## Question 7: What Is `StrictHostKeyChecking`?

**Answer:**

It is an SSH client setting that controls how unknown and changed Host Keys are handled.

---

## Question 8: Is `StrictHostKeyChecking` Configured in `sshd_config`?

**Answer:**

No. It is a client setting configured in:

```text
~/.ssh/config
```

or:

```text
/etc/ssh/ssh_config
```

---

# 29. Host-Key Verification Flow

```text
Client Connects to Server
        │
        ▼
Server Presents Host Key
        │
        ▼
Client Checks known_hosts
        │
        ├── No Existing Key
        │       │
        │       └── Ask User or Apply Client Policy
        │
        ├── Matching Key
        │       │
        │       └── Continue Connection
        │
        └── Different Key
                │
                └── Display Security Warning and Reject
```

---

# 30. Difference Between First Connection and Changed Key

| Situation | Meaning | Normal SSH Behavior |
|-----------|---------|---------------------|
| Unknown Host Key | Client has never connected before | Ask for confirmation |
| Matching Host Key | Server identity matches saved key | Continue |
| Changed Host Key | Server identity differs from saved key | Warn and reject |

---

# 🧪 Practice Exercises

## Exercise 1 – Display Server Host Keys

On Server 2:

```bash
ls -l /etc/ssh/ssh_host_*
```

---

## Exercise 2 – Display the ED25519 Fingerprint

```bash
sudo ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub
```

---

## Exercise 3 – Connect from Server 1

```bash
ssh manish@192.168.1.67
```

---

## Exercise 4 – Display the Client's Known Hosts

```bash
cat ~/.ssh/known_hosts
```

---

## Exercise 5 – Find a Stored Host Key

```bash
ssh-keygen -F 192.168.1.67
```

---

## Exercise 6 – Remove a Lab Host Entry

```bash
ssh-keygen -R 192.168.1.67
```

---

## Exercise 7 – Enable Strict Checking for Server 2

Edit:

```bash
vim ~/.ssh/config
```

Add:

```text
Host server2
    HostName 192.168.1.67
    User manish
    StrictHostKeyChecking yes
```

---

## Exercise 8 – Test the Configuration

```bash
ssh server2
```

---

## Exercise 9 – Check the Effective Setting

```bash
ssh -G server2 | grep -i stricthostkeychecking
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – First-Time Connection Prompt

Message:

```text
The authenticity of host cannot be established.
```

Action:

1. Obtain the real server fingerprint.
2. Compare it with the displayed fingerprint.
3. Accept only if it matches.

---

### Scenario 2 – Remote Host Identification Changed

Message:

```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```

Action:

1. Stop and investigate.
2. Confirm whether the server was rebuilt or replaced.
3. Verify the new fingerprint.
4. Remove the old key only after confirmation.

```bash
ssh-keygen -R hostname_or_ip
```

---

### Scenario 3 – Strict Checking Rejects an Unknown Server

Message:

```text
No ED25519 host key is known and you have requested strict checking.
```

Action:

- Obtain and verify the Host Key.
- Add the verified key to `known_hosts`.
- Then reconnect.

---

### Scenario 4 – Wrong `known_hosts` File Permissions

Fix the SSH directory and file permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/known_hosts
```

---

### Scenario 5 – Server Host-Key Files Are Missing

Check:

```bash
ls -l /etc/ssh/ssh_host_*
```

Generate missing keys:

```bash
sudo ssh-keygen -A
```

Then restart the SSH server:

```bash
sudo systemctl restart sshd
```

> Generating new Host Keys will cause existing clients to receive a changed Host-Key warning.

---

### Scenario 6 – Connecting by IP and Hostname Creates Separate Entries

SSH may store separate entries for:

```text
192.168.1.67
```

and:

```text
server2
```

Search both:

```bash
ssh-keygen -F 192.168.1.67
ssh-keygen -F server2
```

---

# ⚠️ Security Best Practices

- Verify Host-Key fingerprints during first connection.
- Do not blindly type `yes`.
- Investigate all changed Host-Key warnings.
- Protect private server Host Keys.
- Never copy private Host Keys to clients.
- Use `StrictHostKeyChecking yes` in high-security environments.
- Use `accept-new` carefully for automation.
- Avoid `StrictHostKeyChecking no` for sensitive systems.
- Use configuration management to distribute trusted Host Keys.
- Keep `known_hosts` protected from unauthorized modification.

---

# 📌 Quick Revision

| Item | Purpose |
|------|---------|
| SSH Host Key | Identifies the SSH server |
| Server private key | Remains secret on the server |
| Server public key | Presented to clients |
| `~/.ssh/known_hosts` | Stores trusted server Host Keys |
| `/etc/ssh/ssh_host_*` | Stores server Host-Key files |
| `ssh-keygen -lf FILE` | Display a key fingerprint |
| `ssh-keygen -F HOST` | Find a saved Host Key |
| `ssh-keygen -R HOST` | Remove a saved Host Key |
| `StrictHostKeyChecking` | Controls client Host-Key verification |
| Host-Key mismatch | Possible rebuild, replacement, or attack |

---

# Common Commands

| Command | Purpose |
|---------|---------|
| `ssh user@server` | Connect to the SSH server |
| `cat ~/.ssh/known_hosts` | View trusted Host Keys |
| `ssh-keygen -F server` | Search `known_hosts` |
| `ssh-keygen -R server` | Remove an old Host-Key entry |
| `ssh-keygen -lf key.pub` | Display a key fingerprint |
| `ls -l /etc/ssh/ssh_host_*` | List server Host Keys |
| `ssh -G server` | Display effective client settings |
| `ssh-keyscan server` | Retrieve a server Host Key |
| `sshd -t` | Validate SSH server configuration |
| `ssh-keygen -A` | Generate missing server Host Keys |

---

# 📖 Key Takeaways

- SSH Host Keys authenticate the server to the client.
- Host Keys are different from user authentication keys.
- The client stores trusted Host Keys in `~/.ssh/known_hosts`.
- The server stores its Host Keys under `/etc/ssh/`.
- The client compares the saved key with the server's current key on every connection.
- A changed Host Key may indicate a legitimate rebuild or a security attack.
- Always verify fingerprints before trusting a new or changed key.
- `StrictHostKeyChecking` is a client-side setting.
- Client configuration changes do not require restarting `sshd`.
- Use `ssh-keygen -R` instead of randomly editing or corrupting `known_hosts`.

---

# 💡 Remember

> **Think of an SSH Host Key as the identity card of the SSH server.**
>
> - The server presents its identity card.
> - The client checks whether it has seen that identity before.
> - If the identity matches, the connection continues.
> - If the identity changes unexpectedly, SSH displays a warning.
>
> **Golden Host-Key Flow:**
>
> ```text
> Server Presents Host Key
>           │
>           ▼
> Client Checks known_hosts
>           │
>           ├── Match ─────► Trust and Continue
>           │
>           ├── New ───────► Verify Before Accepting
>           │
>           └── Changed ───► Stop and Investigate
> ```
>
> **Never ignore an SSH Host-Key warning until you have verified why the server's identity changed.**