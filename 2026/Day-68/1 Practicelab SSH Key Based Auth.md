# MODULE 08 – SSH Practice Lab 1
> **Configure SSH Key-Based Authentication Between Two Linux Servers**

> **Scenario:** Configure passwordless SSH access for the `oradb` user from **Server 1** to **Server 2**.

---

# 📊 SSH Key-Based Authentication Diagram

![SSH Key-Based Authentication](/mnt/data/image(526).png)

The diagram shows:

```text
Server 1
oradb
   │
   │ SSH Key-Based Authentication
   ▼
Server 2
oradb
```

The goal is to allow the `oradb` user on Server 1 to connect to Server 2 without entering the remote account password.

---

# 🎯 Lab Objectives

In this practice lab, you will learn how to:

- Create the same user on two Linux servers.
- Test normal password-based SSH access.
- Generate an SSH Public and Private Key pair.
- Understand the difference between the Private Key and Public Key.
- Copy the Public Key to a remote server.
- Configure passwordless SSH authentication.
- Verify the remote `authorized_keys` file.
- Protect a Private Key with a passphrase.
- Understand the difference between passwordless login and a Private-Key passphrase.
- Troubleshoot common SSH Key-Based Authentication problems.

---

# 📖 Lab Scenario

Assume that you are a Linux System Administrator.

The database team submits the following request:

> The `oradb` user on Server 1 must be able to connect to Server 2 using SSH without entering the `oradb` account password.

The environment contains:

| System | Role | User |
|--------|------|------|
| Server 1 | SSH Client | `oradb` |
| Server 2 | SSH Server | `oradb` |

The same user must exist on both systems.

---

# Important Direction of Access

The required SSH access is:

```text
Server 1 ─────────► Server 2
```

The key pair must be generated on:

```text
Server 1
```

The Public Key must be copied to:

```text
Server 2
```

---

# 1. Lab Requirements

Before starting, confirm:

- Both servers are powered on.
- Both servers can communicate over the network.
- The SSH service is running on Server 2.
- TCP port `22` is allowed through the firewall.
- You have `root` or `sudo` access.
- The correct hostname or IP address of Server 2 is known.

---

# 2. Example Lab Environment

Replace these values with your actual systems.

| Item | Server 1 | Server 2 |
|------|----------|----------|
| Hostname | `server1` | `server2` |
| IP Address | `192.168.1.66` | `192.168.1.67` |
| User | `oradb` | `oradb` |

---

# 3. Verify the Servers

On Server 1:

```bash
hostname
```

```bash
hostname -I
```

On Server 2:

```bash
hostname
```

```bash
hostname -I
```

Example:

```text
Server 1: 192.168.1.66
Server 2: 192.168.1.67
```

---

# 4. Test Network Connectivity

From Server 1, test Server 2:

```bash
ping -c 4 192.168.1.67
```

Expected result:

```text
4 packets transmitted, 4 received
```

If the server does not respond, verify:

- IP address
- Network configuration
- Routing
- Firewall
- VM power state

---

# 5. Check the SSH Service on Server 2

On Server 2:

```bash
systemctl status sshd
```

If the service is not running:

```bash
sudo systemctl enable --now sshd
```

Verify:

```bash
systemctl is-active sshd
```

Expected Output:

```text
active
```

---

# 6. Check the Firewall on Server 2

Run:

```bash
sudo firewall-cmd --list-services
```

The output should include:

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

# 7. Create the `oradb` User on Server 1

On Server 1, log in as `root` or use `sudo`.

Create the user:

```bash
sudo useradd oradb
```

Set a password:

```bash
sudo passwd oradb
```

Verify:

```bash
id oradb
```

Example Output:

```text
uid=1001(oradb) gid=1001(oradb) groups=1001(oradb)
```

---

# 8. Create the `oradb` User on Server 2

On Server 2:

```bash
sudo useradd oradb
```

Set a password:

```bash
sudo passwd oradb
```

Verify:

```bash
id oradb
```

---

# Important Note About the User

The `oradb` account on Server 1 and the `oradb` account on Server 2 are separate local accounts.

Each server has its own:

- Password
- UID
- Group
- Home directory
- SSH configuration
- Authorized keys

The usernames may be the same, but the accounts are managed independently.

---

# 9. Switch to `oradb` on Server 1

Run:

```bash
su - oradb
```

Verify the user:

```bash
whoami
```

Expected Output:

```text
oradb
```

Check the current directory:

```bash
pwd
```

Expected Output:

```text
/home/oradb
```

---

# 10. Test Password-Based SSH Login

From Server 1 as `oradb`, connect to Server 2:

```bash
ssh oradb@192.168.1.67
```

On the first connection, you may see:

```text
The authenticity of host '192.168.1.67' cannot be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

After verifying the Host-Key fingerprint, type:

```text
yes
```

You will then be asked for the remote account password:

```text
oradb@192.168.1.67's password:
```

Enter the password configured for `oradb` on Server 2.

---

# 11. Verify the Remote Login

After connecting, run:

```bash
whoami
```

Expected:

```text
oradb
```

Run:

```bash
hostname
```

Expected:

```text
server2
```

Run:

```bash
pwd
```

Expected:

```text
/home/oradb
```

Exit back to Server 1:

```bash
exit
```

---

# Current Situation

At this stage, SSH works, but it still asks for the remote account password.

```text
Server 1
oradb
   │
   │ ssh oradb@server2
   ▼
Server 2
oradb
   │
   └── Remote password required
```

The next step is to configure SSH Key-Based Authentication.

---

# 12. Generate an SSH Key Pair on Server 1

Make sure you are logged in as:

```text
oradb
```

Verify:

```bash
whoami
```

Generate a modern ED25519 key pair:

```bash
ssh-keygen -t ed25519
```

---

# Key Generation Prompts

You may see:

```text
Generating public/private ed25519 key pair.
```

Then:

```text
Enter file in which to save the key (/home/oradb/.ssh/id_ed25519):
```

Press:

```text
Enter
```

to accept the default location.

Next:

```text
Enter passphrase (empty for no passphrase):
```

For the first part of this lab, press:

```text
Enter
```

to leave the passphrase empty.

Confirm again by pressing:

```text
Enter
```

---

# 13. Files Created by `ssh-keygen`

Run:

```bash
ls -la ~/.ssh
```

Expected files:

```text
id_ed25519
id_ed25519.pub
```

---

# Meaning of the Key Files

| File | Purpose |
|------|---------|
| `id_ed25519` | Private Key |
| `id_ed25519.pub` | Public Key |

---

# 14. Private Key

The Private Key is:

```text
~/.ssh/id_ed25519
```

It must:

- Remain on Server 1.
- Remain owned by `oradb`.
- Never be copied to Server 2.
- Never be shared with another person.
- Have restrictive permissions.

Check:

```bash
ls -l ~/.ssh/id_ed25519
```

Typical permissions:

```text
-rw-------
```

Set them if necessary:

```bash
chmod 600 ~/.ssh/id_ed25519
```

---

# ⚠️ Private Key Security

Anyone who obtains the Private Key may be able to connect to every server where its matching Public Key is trusted.

Never:

- Email the Private Key.
- Copy it into a ticket.
- Upload it to GitHub.
- Store it in a shared folder.
- Send it through chat.
- Copy it to the destination server.

---

# 15. Public Key

The Public Key is:

```text
~/.ssh/id_ed25519.pub
```

View it:

```bash
cat ~/.ssh/id_ed25519.pub
```

Example:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... oradb@server1
```

The Public Key can safely be copied to Server 2.

---

# 16. Copy the Public Key to Server 2

Use:

```bash
ssh-copy-id oradb@192.168.1.67
```

You may be asked for the Server 2 account password one final time:

```text
oradb@192.168.1.67's password:
```

After successful installation, you should see a message similar to:

```text
Number of key(s) added: 1
```

---

# Specify the Exact Public Key

If multiple key pairs exist, specify the key:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub oradb@192.168.1.67
```

---

# What Does `ssh-copy-id` Do?

The command:

```bash
ssh-copy-id oradb@192.168.1.67
```

normally performs these tasks on Server 2:

1. Creates the remote `.ssh` directory if required.
2. Creates the `authorized_keys` file if required.
3. Copies the Public Key into `authorized_keys`.
4. Applies suitable permissions.
5. Preserves existing authorized keys.

---

# 17. Test Passwordless SSH Login

From Server 1:

```bash
ssh oradb@192.168.1.67
```

If the configuration is correct:

- The remote `oradb` account password is not requested.
- The SSH session opens directly.
- You log in as `oradb` on Server 2.

Verify:

```bash
whoami
hostname
pwd
```

Expected:

```text
oradb
server2
/home/oradb
```

---

# Passwordless Login Flow

```text
Server 1
oradb
Private Key
   │
   │ ssh oradb@server2
   ▼
Server 2
oradb
authorized_keys
   │
   └── Public Key Matches
            │
            ▼
      Login Is Allowed
```

---

# 18. Verify the Public Key on Server 2

While logged in to Server 2 as `oradb`, run:

```bash
ls -la ~/.ssh
```

You should see:

```text
authorized_keys
```

Display its contents:

```bash
cat ~/.ssh/authorized_keys
```

The line should match the Public Key from Server 1:

```bash
cat ~/.ssh/id_ed25519.pub
```

Run the second command on Server 1.

---

# Important Clarification

The Public Key is not copied as a separate file named:

```text
id_ed25519.pub
```

on Server 2.

Instead, its contents are appended to:

```text
~/.ssh/authorized_keys
```

---

# 19. Verify Remote Permissions

On Server 2:

```bash
ls -ld ~/.ssh
```

```bash
ls -l ~/.ssh/authorized_keys
```

Recommended permissions:

```text
~/.ssh                 700
~/.ssh/authorized_keys 600
```

Apply them if required:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Verify ownership:

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```

The owner should be:

```text
oradb:oradb
```

Fix if required:

```bash
sudo chown -R oradb:oradb /home/oradb/.ssh
```

---

# 20. Restore SELinux Contexts

On Rocky Linux or RHEL, restore the correct SELinux labels:

```bash
sudo restorecon -Rv /home/oradb/.ssh
```

This is useful if the directory or files were created or copied manually.

---

# 21. How Authentication Works Behind the Scenes

A simplified SSH Key-Based Authentication process is:

1. `oradb` on Server 1 starts an SSH connection.
2. Server 2 presents its SSH Host Key.
3. Server 1 verifies Server 2 using `known_hosts`.
4. Server 1 offers the `oradb` Public Key identity.
5. Server 2 checks `/home/oradb/.ssh/authorized_keys`.
6. Server 2 confirms that the Public Key is authorized.
7. Server 1 proves possession of the matching Private Key by creating a cryptographic signature.
8. Server 2 verifies the signature using the Public Key.
9. Login is allowed.

---

# Authentication Flow

```text
Client Offers Public Key
        │
        ▼
Server Checks authorized_keys
        │
        ├── Key Missing
        │      └── Authentication Fails
        │
        └── Key Found
               │
               ▼
Client Signs Authentication Data
Using the Private Key
               │
               ▼
Server Verifies the Signature
Using the Public Key
               │
               ▼
Authentication Succeeds
```

---

# Important Security Fact

The Private Key:

```text
~/.ssh/id_ed25519
```

is never transmitted to Server 2.

It stays on Server 1.

Only cryptographic proof is sent.

---

# 22. Create a Second User with a Protected Private Key

Now create another user named:

```text
oracle
```

This part demonstrates a Private Key protected by a passphrase.

---

# 23. Create `oracle` on Server 1

On Server 1:

```bash
sudo useradd oracle
```

Set a password:

```bash
sudo passwd oracle
```

Verify:

```bash
id oracle
```

---

# 24. Create `oracle` on Server 2

On Server 2:

```bash
sudo useradd oracle
```

Set a password:

```bash
sudo passwd oracle
```

Verify:

```bash
id oracle
```

---

# 25. Switch to `oracle` on Server 1

Run:

```bash
su - oracle
```

Verify:

```bash
whoami
```

Expected:

```text
oracle
```

---

# 26. Generate a Passphrase-Protected Key

Run:

```bash
ssh-keygen -t ed25519
```

Accept the default file location.

When prompted:

```text
Enter passphrase (empty for no passphrase):
```

enter a strong passphrase.

Example:

```text
Do not use simple classroom passwords in production.
```

Confirm the same passphrase.

---

# What Does the Passphrase Protect?

The passphrase protects the local Private Key file:

```text
~/.ssh/id_ed25519
```

It does not change the remote account password.

It does not get copied to Server 2.

---

# 27. Copy Oracle's Public Key

From Server 1 as `oracle`:

```bash
ssh-copy-id oracle@192.168.1.67
```

Enter the remote `oracle` account password one final time.

---

# 28. Test Oracle's SSH Login

Run:

```bash
ssh oracle@192.168.1.67
```

You may see:

```text
Enter passphrase for key '/home/oracle/.ssh/id_ed25519':
```

Enter the Private-Key passphrase.

The remote account password should not be requested.

---

# Passphrase vs Remote Password

| Credential | What It Protects |
|------------|------------------|
| Remote account password | The `oracle` account on Server 2 |
| Private-Key passphrase | The Private Key on Server 1 |

---

# Important Clarification

A passphrase-protected SSH key is still Key-Based Authentication.

It is not the same as password authentication.

You are unlocking the local Private Key, not authenticating with the remote user's password.

---

# 29. Why Use a Private-Key Passphrase?

Suppose an attacker steals:

```text
/home/oracle/.ssh/id_ed25519
```

If the key has no passphrase, the attacker may immediately attempt to use it.

If the key is protected by a strong passphrase, the attacker also needs that passphrase.

This provides an additional layer of protection.

---

# 30. Use `ssh-agent` to Avoid Repeated Passphrase Prompts

Start an SSH agent:

```bash
eval "$(ssh-agent -s)"
```

Add the key:

```bash
ssh-add ~/.ssh/id_ed25519
```

Enter the passphrase once.

Verify loaded keys:

```bash
ssh-add -l
```

Now connect:

```bash
ssh oracle@192.168.1.67
```

During the current agent session, SSH may not ask for the passphrase again.

---

# 31. Compare the Two Lab Users

| User | Private-Key Passphrase | Remote Password Requested After Setup |
|------|-------------------------|---------------------------------------|
| `oradb` | No | No |
| `oracle` | Yes | No |
| `oracle` with `ssh-agent` | Yes, entered into agent | No repeated prompt during agent session |

---

# 32. Verify Which Key SSH Uses

Run:

```bash
ssh -v oradb@192.168.1.67
```

Look for:

```text
Offering public key
Server accepts key
Authenticated using "publickey"
```

For additional detail:

```bash
ssh -vv oradb@192.168.1.67
```

---

# 33. Use a Specific Private Key

If several keys exist:

```bash
ssh -i ~/.ssh/id_ed25519 oradb@192.168.1.67
```

For a custom key:

```bash
ssh -i ~/.ssh/oradb_server2 oradb@192.168.1.67
```

---

# 34. Configure an SSH Client Alias

On Server 1 as `oradb`, edit:

```bash
vim ~/.ssh/config
```

Add:

```text
Host database-server
    HostName 192.168.1.67
    User oradb
    IdentityFile ~/.ssh/id_ed25519
```

Secure the file:

```bash
chmod 600 ~/.ssh/config
```

Connect using:

```bash
ssh database-server
```

---

# 35. Check Server-Side Public-Key Authentication

On Server 2:

```bash
sudo sshd -T | grep -i pubkeyauthentication
```

Expected Output:

```text
pubkeyauthentication yes
```

Also check the authorized-keys location:

```bash
sudo sshd -T | grep -i authorizedkeysfile
```

Typical Output:

```text
authorizedkeysfile .ssh/authorized_keys .ssh/authorized_keys2
```

---

# 36. Validate SSH Server Configuration

Before restarting `sshd`:

```bash
sudo sshd -t
```

If there is no output, the syntax is generally valid.

Restart only if server configuration was changed:

```bash
sudo systemctl restart sshd
```

---

# 37. Optional: Disable Password Authentication

Only after confirming that Key-Based Authentication works:

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

Before disabling passwords:

1. Keep the current SSH session open.
2. Open a second terminal.
3. Test Key-Based Authentication.
4. Confirm administrative access.
5. Confirm the correct key is offered.
6. Confirm firewall access.
7. Do not close the working session until the new login succeeds.

---

# 38. Complete Lab Workflow

```text
Create User on Server 1 and Server 2
                │
                ▼
Test Password-Based SSH Login
                │
                ▼
Generate Key Pair on Server 1
                │
                ├── Private Key stays on Server 1
                │
                └── Public Key copied to Server 2
                                │
                                ▼
                  ~/.ssh/authorized_keys
                                │
                                ▼
                     Test SSH Login Again
                                │
                                ▼
                 No Remote Password Required
```

---

# 🧪 Student Practice Tasks

## Task 1 – Create `oradb`

On both servers:

```bash
sudo useradd oradb
sudo passwd oradb
```

---

## Task 2 – Verify the User

```bash
id oradb
```

---

## Task 3 – Test Normal SSH

From Server 1:

```bash
su - oradb
ssh oradb@192.168.1.67
```

---

## Task 4 – Generate a Key Pair

```bash
ssh-keygen -t ed25519
```

---

## Task 5 – Verify the Key Files

```bash
ls -la ~/.ssh
```

---

## Task 6 – Copy the Public Key

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub oradb@192.168.1.67
```

---

## Task 7 – Test Key-Based Login

```bash
ssh oradb@192.168.1.67
```

---

## Task 8 – Verify the Remote File

On Server 2:

```bash
cat ~/.ssh/authorized_keys
```

---

## Task 9 – Verify Permissions

```bash
ls -ld ~/.ssh
ls -l ~/.ssh/authorized_keys
```

---

## Task 10 – Test Verbose Authentication

```bash
ssh -v oradb@192.168.1.67
```

---

# 🔧 Troubleshooting Scenarios

### Scenario 1 – SSH Still Requests the Remote Password

Run:

```bash
ssh -vv oradb@192.168.1.67
```

Possible causes:

- Wrong Public Key copied.
- Wrong remote user.
- Incorrect permissions.
- Incorrect ownership.
- Public-Key authentication disabled.
- Wrong Private Key offered.
- SELinux context problem.

---

### Scenario 2 – Fix Remote Permissions

On Server 2:

```bash
chmod 700 /home/oradb/.ssh
chmod 600 /home/oradb/.ssh/authorized_keys
sudo chown -R oradb:oradb /home/oradb/.ssh
```

---

### Scenario 3 – Restore SELinux Context

```bash
sudo restorecon -Rv /home/oradb/.ssh
```

---

### Scenario 4 – `ssh-copy-id` Is Missing

Install:

```bash
sudo dnf install -y openssh-clients
```

Or copy manually:

```bash
cat ~/.ssh/id_ed25519.pub | ssh oradb@192.168.1.67 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys'
```

---

### Scenario 5 – Wrong Private Key Is Being Offered

Specify the key:

```bash
ssh -i ~/.ssh/id_ed25519 oradb@192.168.1.67
```

Debug:

```bash
ssh -vv -i ~/.ssh/id_ed25519 oradb@192.168.1.67
```

---

### Scenario 6 – Permission Denied (Publickey)

Check on Server 2:

```bash
sudo journalctl -u sshd -n 50
```

or:

```bash
sudo tail -n 50 /var/log/secure
```

---

### Scenario 7 – Public Key Was Copied to the Wrong Account

Confirm:

```bash
ssh-copy-id oradb@192.168.1.67
```

The key must exist in:

```text
/home/oradb/.ssh/authorized_keys
```

not:

```text
/root/.ssh/authorized_keys
```

or another user's home directory.

---

### Scenario 8 – Duplicate Keys in `authorized_keys`

Check:

```bash
sort ~/.ssh/authorized_keys | uniq -d
```

Duplicate keys normally do not break authentication, but they make management confusing.

Remove unnecessary duplicates carefully.

---

### Scenario 9 – Private Key Permissions Are Too Open

Error may include:

```text
WARNING: UNPROTECTED PRIVATE KEY FILE!
```

Fix:

```bash
chmod 600 ~/.ssh/id_ed25519
```

---

### Scenario 10 – Passphrase Is Forgotten

The Private-Key passphrase cannot normally be recovered.

You must:

1. Generate a new key pair.
2. Copy the new Public Key to Server 2.
3. Remove the old Public Key from `authorized_keys`.

---

# ⚠️ Security Best Practices

- Use ED25519 keys when supported.
- Protect interactive user keys with a passphrase.
- Keep Private Keys only on trusted client systems.
- Never share Private Keys.
- Use separate keys for different purposes when practical.
- Use dedicated service accounts for automation.
- Restrict automation keys in `authorized_keys`.
- Remove unused Public Keys.
- Review `authorized_keys` regularly.
- Disable password authentication only after successful testing.
- Use `ssh-agent` carefully.
- Back up critical keys securely and according to organizational policy.

---

# 📌 Quick Revision

| Item | Purpose |
|------|---------|
| `ssh-keygen` | Generate Public and Private Keys |
| Private Key | Stays on Server 1 |
| Public Key | Copied to Server 2 |
| `ssh-copy-id` | Install the Public Key remotely |
| `authorized_keys` | Stores approved Public Keys |
| Passphrase | Protects the local Private Key |
| Remote password | Protects the remote user account |
| `ssh-agent` | Stores an unlocked key temporarily |
| `ssh -v` | Troubleshoots SSH authentication |
| `restorecon` | Restores SELinux file contexts |

---

# Common Commands

| Command | Purpose |
|---------|---------|
| `ssh-keygen -t ed25519` | Generate an ED25519 key pair |
| `ssh-copy-id user@server` | Copy the Public Key |
| `ssh-copy-id -i key.pub user@server` | Copy a specific Public Key |
| `ssh user@server` | Test Key-Based Authentication |
| `ssh -i key user@server` | Use a specific Private Key |
| `ssh -v user@server` | Display authentication details |
| `cat ~/.ssh/authorized_keys` | View approved Public Keys |
| `chmod 700 ~/.ssh` | Secure the SSH directory |
| `chmod 600 ~/.ssh/authorized_keys` | Secure the authorized-keys file |
| `restorecon -Rv ~/.ssh` | Restore SELinux contexts |

---

# 📖 Key Takeaways

- The `oradb` user must exist on both servers.
- The key pair is generated on the source system, Server 1.
- The Private Key remains on Server 1.
- The Public Key is copied to Server 2.
- `ssh-copy-id` places the Public Key in `authorized_keys`.
- After successful configuration, the remote account password is not required.
- A key passphrase protects the local Private Key.
- The Private Key is never sent across the network.
- Correct permissions, ownership, and SELinux contexts are essential.
- Always test the connection before disabling password authentication.

---

# 💡 Remember

> **Think of SSH Key-Based Authentication as a secure identity-proof system.**
>
> - Server 1 keeps the **Private Key**.
> - Server 2 stores the matching **Public Key**.
> - Server 1 proves that it owns the Private Key.
> - Server 2 verifies that proof with the Public Key.
>
> **Golden Rule:**
>
> ```text
> Generate Keys on the Source Server
>
> Keep the Private Key on the Source Server
>
> Copy the Public Key to authorized_keys on the Destination Server
> ```
>
> **Never copy the Private Key to the destination server.**