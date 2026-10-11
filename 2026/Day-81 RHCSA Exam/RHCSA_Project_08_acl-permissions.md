# NexusVentures Project 08: Fine-Grained File Access with ACLs

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Copy `/etc/fstab` to `/var/tmp/fstab`; retain root ownership; remove execute access; grant Harry read/write; deny Natasha; and allow all other users to read.

## 2. Business Scenario

NexusVentures needs user-specific exceptions that normal owner/group/other mode bits cannot express.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 04 must have created Harry, Natasha, and Sarah.

## 5. Step-by-Step Solution

### Step 1: Install ACL tools and create the copy

```bash
dnf install -y acl
cp -p /etc/fstab /var/tmp/fstab
chown root:root /var/tmp/fstab
chmod 0644 /var/tmp/fstab
setfacl -b /var/tmp/fstab
```

### Step 2: Add named-user entries

```bash
setfacl -m u:harry:rw- /var/tmp/fstab
setfacl -m u:natasha:--- /var/tmp/fstab
setfacl -m m::rw- /var/tmp/fstab
```

### Step 3: Inspect

```bash
ls -l /var/tmp/fstab
getfacl /var/tmp/fstab
```

The `+` in `ls -l` indicates extended ACL information.

### Step 4: Test Harry

```bash
runuser -u harry -- head -n 1 /var/tmp/fstab
runuser -u harry -- sh -c 'echo "# Harry ACL test" >> /var/tmp/fstab'
```

### Step 5: Test Natasha

```bash
runuser -u natasha -- cat /var/tmp/fstab
runuser -u natasha -- sh -c 'echo fail >> /var/tmp/fstab'
```

Both must fail.

### Step 6: Test another user

```bash
runuser -u sarah -- head -n 1 /var/tmp/fstab
```

Sarah reads through `other::r--`.

### Step 7: Confirm no execute bit

```bash
find /var/tmp/fstab -perm /111 -print
```

No output is expected.

### Step 8: Save evidence

```bash
mkdir -p /root/nexusventures-project08/evidence
getfacl /var/tmp/fstab > /root/nexusventures-project08/evidence/fstab-acl.txt
stat /var/tmp/fstab > /root/nexusventures-project08/evidence/fstab-stat.txt
```

## 6. Required Validation

```bash
test "$(stat -c '%U:%G' /var/tmp/fstab)" = "root:root"
! find /var/tmp/fstab -perm /111 | grep -q .
getfacl /var/tmp/fstab | grep 'user:harry:rw-'
getfacl /var/tmp/fstab | grep 'user:natasha:---'
getfacl /var/tmp/fstab | grep 'other::r--'
runuser -u harry -- test -w /var/tmp/fstab
! runuser -u natasha -- test -r /var/tmp/fstab
runuser -u sarah -- test -r /var/tmp/fstab
```

## 7. Evidence Students Must Submit

Submit `ls -l`, `getfacl`, owner/group, Harry read/write proof, Natasha denial, Sarah read proof, and explanation of the ACL mask.

## 8. Rollback or Cleanup

```bash
rm -f /var/tmp/fstab
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
