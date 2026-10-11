# NexusVentures Project 14: Root Password Recovery through GRUB

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Reset a forgotten root password by interrupting GRUB, using `rd.break`, entering a chroot, and requesting SELinux relabeling.

## 2. Business Scenario

A NexusVentures training server has lost root credentials. Students must recover authorized access without reinstalling the OS.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Use only an assigned training VM. Unauthorized password recovery is prohibited.

## 5. Step-by-Step Solution

### Step 1: Create a snapshot

Use a dedicated recovery VM and create `PRE-ROOT-RECOVERY-PROJECT14`.

### Step 2: Use the Xen Orchestra console

Reboot. At the GRUB menu:

1. Highlight the normal Rocky Linux entry.
2. Press `e`.
3. Find the line beginning with `linux`, `linuxefi`, or similar.
4. Append `rd.break`.
5. Press `Ctrl+x`.

### Step 3: Remount the real root filesystem

At the emergency prompt:

```bash
mount -o remount,rw /sysroot
mount | grep ' /sysroot '
```

### Step 4: Enter the installed system

```bash
chroot /sysroot
```

### Step 5: Reset root

```bash
passwd root
```

Use the instructor-approved temporary password. Do not include the password in submitted evidence.

### Step 6: Request SELinux relabeling

```bash
touch /.autorelabel
ls -l /.autorelabel
```

### Step 7: Exit twice

```bash
exit
exit
```

The relabel can take several minutes.

### Step 8: Validate console login

Log in as root, then:

```bash
whoami
id
getenforce
ls -lZ /etc/shadow
systemctl --failed
```

### Step 9: Perform a normal reboot

```bash
reboot
```

Confirm normal root login again without editing GRUB.

## 6. Required Validation

Students must prove successful root login, `uid=0(root)`, SELinux enforcing, a valid context on `/etc/shadow`, no unexplained failed services, and a second normal boot.

## 7. Evidence Students Must Submit

Submit snapshot name, written GRUB sequence, `/sysroot` remount, chroot, proof of `/.autorelabel`, root identity after recovery, SELinux state, shadow-file context, and normal reboot proof. Never submit the password.

## 8. Rollback or Cleanup

Set another approved password with `passwd root` or revert the pre-project snapshot if the VM is damaged.

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
