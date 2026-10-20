# Exam Question #15: Configure SUDO Access


## 1. Exam Question #14
On **Node1**, perform the following tasks:
   * Create a new user jane and ensure that jane can execute commands with `sudo`
   * Configure the system so that all memebers of the group `admins` can execute commands with `sudo` without being prompted for a password

## Exam Question #15 Solution
### Solution Question 1:
* This portion of the exam is testing how to add a user to `wheel` group. Thus, giving the user SUDO Previliges/rights/access.
```bash
useradd jane
passwd jane
#for password use jane

#Let us login as jane
su - jane
[jane@RedhatExam ~]$sudo useradd testuser2
[jane@RedhatExam ~]$ sudo useradd jane
#You will see the below message
We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.
[sudo] password for jane:
jane is not in the sudoers file.  This incident will be reported.

# Let us test another file accesss as sudo
[jane@RedhatExam ~]$ sudo ls /root
[sudo] password for jane:
jane is not in the sudoers file.  This incident will be reported.

# Go back to becoming root user
[jane@RedhatExam ~]$ exit
logout
#Add to Jane to wheel group so that she has SUDO ACCESS!
[root@RedhatExam ~]# usermod -aG wheel jane

```
### Validate
```bash
su - jane
sudo ls /root
# This should work now
```
### Check if a User is in wheel group:
* The `groups` command is used in Linux/Unix to print the groups that a specific user (or the current user) belongs to.
* While `lid -g wheel` checks which users are in a specific group.
* Understand `Who Where = (As Who)  What (Commands)`

### Solution Question 2:
* Putting rules directly in `/etc/sudoers` file using `visudo` and add your rules
```bash
visudo
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL

## Allows members of the 'sys' group to run networking, software,
## service management apps and more.
# %sys ALL = NETWORKING, SOFTWARE, SERVICES, STORAGE, DELEGATING, PROCESSES, LOCATE, DRIVERS

## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL

## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL
%admins         ALL=(ALL)       NOPASSWD: ALL /etc/sudoers.d/admins
:wq!
```

### Validate
```bash
[root@RedhatExam ~]# id harry
uid=3455(harry) gid=3500(admins) groups=3500(admins),100(users)

[root@RedhatExam ~]# su - harry
[harry@RedhatExam ~]# su - harry
```

IMPORTANT NOTE 1
`The Catch: File Permissions`
If you just run echo "..." >> /etc/sudoers.d/admins, the system will create the file using your default umask (usually 0644).

As mentioned earlier, `sudo is extremely strict`. If a file inside /etc/sudoers.d/ is writable by regular users or has the wrong permissions/ownership, sudo will throw an error and completely refuse to work to prevent security vulnerabilities.

`The Safest Ways to Do It - The Recommended Way (visudo)`
Using visudo handles the temporary file creation and syntax checking safely:

Bash
sudo visudo -f /etc/sudoers.d/admins
Then paste your line, save, and exit. visudo automatically ensures the permissions are set to 0440 and owned by root.

IMPORTANT NOTE 2
`Understanding `sudoers` Syntax & Rules`
* Basic Syntax Breakdown

Every rule line in `/etc/sudoers` follows this formula:

$$
\text{Who} \quad \text{Where} = (\text{As Who}) \quad \text{What (Commands)}
$$

| Symbol / Keyword | Meaning | 
| ----- | ----- | 
| **`%` Prefix** | Indicates a **group** rather than an individual username (e.g., `%wheel` or `%admins`). | 
| **`ALL` (1st)** | Applies to all hostnames/networks. | 
| **`(ALL)`** | Allows executing commands as any user (or group). | 
| **`NOPASSWD:`** | Suppresses the password prompt for matching commands. | 
| **`ALL` (2nd)** | Allows running **all** executable commands. | 

#### Example Comparison

* `%wheel ALL=(ALL) ALL`: Grants full `sudo` access to every user in the `wheel` group (requires password).
* `wheel ALL=(ALL) ALL`: Grants full `sudo` access **only** to an individual user named `wheel`.
* `%admins ALL=(ALL) NOPASSWD: ALL`: Grants full `sudo` access to every member of the `admins` group without requiring a password.

---
> The Modular Approach: `/etc/sudoers.d/`
* Direct Editing vs. Drop-in Files

1. **Direct Editing (`/etc/sudoers`):**
   * Can become unorganized and cluttered over time.
   * Can cause conflicts during system/package updates.

2. **Drop-in Files (`/etc/sudoers.d/`):**
   * Modular and safe.
   * Enables configuration tools (Ansible, Puppet, shell scripts) to manage permissions cleanly.
   * Read automatically via the `#includedir /etc/sudoers.d` line in `/etc/sudoers`.