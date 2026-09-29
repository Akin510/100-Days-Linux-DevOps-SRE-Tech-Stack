# NexusVentures Project 04: Identity and Administrative Group Provisioning

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create group `admin`; add `harry` and `natasha` as supplementary members; create `sarah` with `/sbin/nologin` and no `admin` membership; assign training passwords.

## 2. Business Scenario

NexusVentures is onboarding two junior administrators and one noninteractive service identity.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project04/`.


## 5. Step-by-Step Solution

### Step 1: Create the group

```bash
groupadd admin
```

If repeating the lab, first use `getent group admin`.

### Step 2: Create Harry and Natasha

```bash
useradd -m -G admin harry
passwd harry
useradd -m -G admin natasha
passwd natasha
```

Use the instructor-assigned lab password. The word `password` must never be used in real environments.

### Step 3: Create Sarah

```bash
useradd -m -s /sbin/nologin sarah
passwd sarah
```

### Step 4: Validate account data

```bash
id harry
id natasha
id sarah
getent group admin
getent passwd harry natasha sarah
pwck -r
grpck -r
```

### Step 5: Test Sarah’s restriction

```bash
runuser -l sarah -c 'id'
```

An account-unavailable message is expected.

### Step 6: Save evidence

```bash
mkdir -p /root/nexusventures-project04/evidence
id harry > /root/nexusventures-project04/evidence/harry.txt
id natasha > /root/nexusventures-project04/evidence/natasha.txt
id sarah > /root/nexusventures-project04/evidence/sarah.txt
getent group admin > /root/nexusventures-project04/evidence/admin-group.txt
```

## 6. Required Validation

```bash
getent group admin
id harry | grep admin
id natasha | grep admin
! id sarah | grep -q admin
getent passwd sarah | grep '/sbin/nologin$'
pwck -r
grpck -r
```

## 7. Evidence Students Must Submit

Submit `id` output for all users, the admin group record, passwd records, Sarah’s failed interactive-shell test, and an explanation of primary versus supplementary groups.

## 8. Rollback or Cleanup

Only after dependent projects are finished:

```bash
userdel -r harry
userdel -r natasha
userdel -r sarah
groupdel admin
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
