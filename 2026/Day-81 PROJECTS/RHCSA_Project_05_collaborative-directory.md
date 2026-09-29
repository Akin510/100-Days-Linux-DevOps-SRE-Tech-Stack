# NexusVentures Project 05: Secure Collaborative Administration Workspace

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create `/common/admin` with group ownership `admin`, full access for group members, no access for others, and automatic group inheritance for new files.

## 2. Business Scenario

NexusVentures administrators need a controlled shared workspace where new files remain associated with the team.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project05/`.
Project 04 is required.

## 5. Step-by-Step Solution

### Step 1: Confirm dependencies

```bash
getent group admin
id harry
id natasha
id sarah
```

### Step 2: Create and configure

```bash
mkdir -p /common/admin
chown root:admin /common/admin
chmod 2770 /common/admin
```

The leading `2` sets the setgid bit. New files inherit group `admin`.

### Step 3: Inspect

```bash
ls -ld /common/admin
stat -c '%A %a %U:%G %n' /common/admin
```

### Step 4: Test authorized users

```bash
runuser -u harry -- touch /common/admin/harry-file
runuser -u natasha -- touch /common/admin/natasha-file
stat -c '%U:%G %n' /common/admin/harry-file /common/admin/natasha-file
```

Expected group: `admin`.

### Step 5: Test Sarah denial

```bash
runuser -u sarah -- ls /common/admin
runuser -u sarah -- touch /common/admin/should-fail
```

Both should fail.

### Step 6: Reboot and repeat

```bash
reboot
```

Then:

```bash
stat -c '%a %U:%G' /common/admin
runuser -u harry -- touch /common/admin/post-reboot-file
stat -c '%G' /common/admin/post-reboot-file
```

## 6. Required Validation

```bash
test "$(stat -c '%a' /common/admin)" = "2770"
test "$(stat -c '%G' /common/admin)" = "admin"
runuser -u harry -- test -w /common/admin
runuser -u natasha -- test -w /common/admin
! runuser -u sarah -- test -r /common/admin
test "$(stat -c '%G' /common/admin/harry-file)" = "admin"
```

## 7. Evidence Students Must Submit

Submit directory mode and ownership, successful creation by both admin members, denial for Sarah, inherited group ownership, and post-reboot evidence.

## 8. Rollback or Cleanup

```bash
rm -rf /common/admin
rmdir /common 2>/dev/null || true
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
