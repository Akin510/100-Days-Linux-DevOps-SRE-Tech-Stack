# NexusVentures Project 11: Identity, Archive, and Private Permission Defaults

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Complete three related tasks: create user `alies` with UID 1326; archive `/var/tmp` as `/root/test.tar.gz`; and configure Natasha’s new files as mode 400 and directories as mode 500 by default.

## 2. Business Scenario

NexusVentures needs a fixed application identity, a compressed evidence backup, and a highly private default workspace for a sensitive user.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 04 must have created Natasha.

## 5. Step-by-Step Solution

## Part A: Create the fixed UID account

### Step 1: Check for conflicts

```bash
getent passwd alies
getent passwd 1326
```

Do not continue if UID 1326 belongs to another account.

### Step 2: Create the account

```bash
useradd -m -u 1326 alies
passwd alies
```

Use an instructor-assigned lab password. The exam example uses `alies`; do not use weak passwords outside the lab.

### Step 3: Validate

```bash
id alies
getent passwd alies
```

## Part B: Archive `/var/tmp`

### Step 4: Create the gzip archive

```bash
tar -czvf /root/test.tar.gz -C / var/tmp
```

Using `-C /` stores the relative path `var/tmp` instead of an absolute path.

### Step 5: Validate the archive

```bash
gzip -t /root/test.tar.gz
tar -tzf /root/test.tar.gz | head -30
ls -lh /root/test.tar.gz
```

### Step 6: Optional restore test

```bash
mkdir -p /root/project11-restore
tar -xzf /root/test.tar.gz -C /root/project11-restore
find /root/project11-restore -maxdepth 3 | head -30
```

## Part C: Configure Natasha’s umask

### Step 7: Back up the profile

```bash
cp -a /home/natasha/.bash_profile   /home/natasha/.bash_profile.before-project11
```

### Step 8: Add umask 0277

```bash
grep -qxF 'umask 0277' /home/natasha/.bash_profile ||   echo 'umask 0277' >> /home/natasha/.bash_profile
chown natasha:natasha /home/natasha/.bash_profile
```

### Step 9: Validate a new login shell

```bash
runuser -l natasha -c 'umask'
```

Expected: `0277`.

### Step 10: Create test objects

```bash
runuser -l natasha -c '
rm -f ~/project11-file
rm -rf ~/project11-directory
touch ~/project11-file
mkdir ~/project11-directory
stat -c "%A %a %U:%G %n" ~/project11-file ~/project11-directory
'
```

Expected:

```text
file: 400
directory: 500
```

Files begin with maximum mode 666 and directories with 777. Applying mask 0277 removes owner write, all group permissions, and all other permissions.

## 6. Required Validation

```bash
test "$(id -u alies)" = "1326"
gzip -t /root/test.tar.gz
tar -tzf /root/test.tar.gz | grep '^var/tmp'
test "$(runuser -l natasha -c 'umask')" = "0277"
test "$(stat -c '%a' /home/natasha/project11-file)" = "400"
test "$(stat -c '%a' /home/natasha/project11-directory)" = "500"
```

## 7. Evidence Students Must Submit

Submit UID availability check, `id alies`, archive size and listing, gzip integrity test, Natasha profile change, umask output, file and directory modes, and an explanation of why duplicate UIDs are dangerous.

## 8. Rollback or Cleanup

```bash
userdel -r alies
rm -f /root/test.tar.gz
rm -rf /root/project11-restore
cp -a /home/natasha/.bash_profile.before-project11 /home/natasha/.bash_profile
rm -f /home/natasha/project11-file
rm -rf /home/natasha/project11-directory
```

## 9. Completion Checklist

- [ ] Correct VM confirmed
- [ ] Snapshot created when required
- [ ] Original state recorded
- [ ] Configuration completed
- [ ] Validation passed
- [ ] SELinux remains enforcing
- [ ] firewalld remains enabled
- [ ] Reboot persistence tested when required
- [ ] Evidence collected
- [ ] Rollback understood

## 10. Review Questions

1. What business problem did this project solve?
2. Which command proved the configuration was active?
3. Which command proved it was persistent?
4. What could fail, and how would you roll back?
