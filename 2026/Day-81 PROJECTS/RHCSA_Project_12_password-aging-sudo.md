# NexusVentures Project 12: Password Aging and Delegated Administration

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Set the default maximum password age for new users to 20 days and allow members of group `admin` to use sudo without a password.

## 2. Business Scenario

NexusVentures is implementing account-lifecycle policy and delegating administration to an approved Linux support group.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 04 must provide group `admin`, Harry, Natasha, and Sarah.

## 5. Step-by-Step Solution

## Part A: Password aging

### Step 1: Back up login defaults

```bash
mkdir -p /root/nexusventures-project12/backups
cp -a /etc/login.defs /root/nexusventures-project12/backups/login.defs-before
```

### Step 2: Edit the default

```bash
vi /etc/login.defs
```

Set:

```text
PASS_MAX_DAYS   20
```

Confirm:

```bash
grep -nE '^[[:space:]]*PASS_MAX_DAYS[[:space:]]+' /etc/login.defs
```

### Step 3: Create a test user

```bash
useradd -m nexusnew
passwd nexusnew
chage -l nexusnew
```

The maximum number of days should be 20. Existing users are not automatically modified.

## Part B: Passwordless sudo for admin group

### Step 4: Install sudo

```bash
dnf install -y sudo
```

### Step 5: Create a drop-in

```bash
cat > /etc/sudoers.d/nexus-admin <<'EOF'
%admin ALL=(ALL) NOPASSWD: ALL
EOF
chown root:root /etc/sudoers.d/nexus-admin
chmod 0440 /etc/sudoers.d/nexus-admin
```

### Step 6: Validate syntax before testing

```bash
visudo -cf /etc/sudoers
visudo -cf /etc/sudoers.d/nexus-admin
```

### Step 7: Review effective privileges

```bash
sudo -l -U harry
sudo -l -U natasha
```

### Step 8: Test Harry

```bash
runuser -l harry -c 'sudo -n /usr/bin/id'
```

Expected output contains `uid=0(root)`.

### Step 9: Prove Sarah has no privilege

```bash
runuser -l sarah -c 'sudo -n /usr/bin/id'
```

This must fail.

### Step 10: Reboot and retest

```bash
reboot
```

After reboot:

```bash
chage -l nexusnew
visudo -cf /etc/sudoers
runuser -l harry -c 'sudo -n /usr/bin/id'
```

## 6. Required Validation

```bash
grep -Eq '^PASS_MAX_DAYS[[:space:]]+20$' /etc/login.defs
chage -l nexusnew | grep -i 'Maximum number of days' | grep 20
visudo -cf /etc/sudoers
test "$(stat -c '%a' /etc/sudoers.d/nexus-admin)" = "440"
runuser -l harry -c 'sudo -n /usr/bin/id' | grep 'uid=0'
! runuser -l sarah -c 'sudo -n /usr/bin/id'
```

## 7. Evidence Students Must Submit

Submit login.defs before and after, `chage -l nexusnew`, sudoers drop-in and permissions, `visudo` output, Harry success, Sarah denial, and reboot test. Explain the risk of `NOPASSWD: ALL`.

## 8. Rollback or Cleanup

```bash
cp -a /root/nexusventures-project12/backups/login.defs-before /etc/login.defs
rm -f /etc/sudoers.d/nexus-admin
userdel -r nexusnew
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
