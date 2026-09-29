# NexusVentures Project 18: Automatic Performance Profile Selection with TuneD

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Install TuneD and activate the profile recommended for the system.

## 2. Business Scenario

NexusVentures wants the operating system to apply an appropriate performance and power-management policy for each VM.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.


## 5. Step-by-Step Solution

### Step 1: Record system type

```bash
lscpu
systemd-detect-virt
```

### Step 2: Install and enable TuneD

```bash
dnf install -y tuned
systemctl enable --now tuned
systemctl status tuned --no-pager
```

### Step 3: Record current profile

```bash
mkdir -p /root/nexusventures-project18/evidence
tuned-adm active > /root/nexusventures-project18/evidence/profile-before.txt
```

### Step 4: Query recommendation

```bash
RECOMMENDED_PROFILE=$(tuned-adm recommend)
echo "$RECOMMENDED_PROFILE"
tuned-adm list
```

Virtual machines often receive `virtual-guest`, but use the actual recommendation.

### Step 5: Apply and verify

```bash
tuned-adm profile "$RECOMMENDED_PROFILE"
tuned-adm active
tuned-adm verify
systemctl is-active tuned
systemctl is-enabled tuned
```

### Step 6: Save evidence

```bash
tuned-adm recommend > /root/nexusventures-project18/evidence/recommended.txt
tuned-adm active > /root/nexusventures-project18/evidence/profile-after.txt
tuned-adm verify > /root/nexusventures-project18/evidence/verify.txt
```

### Step 7: Reboot and retest

```bash
reboot
```

Then:

```bash
systemctl is-active tuned
tuned-adm active
tuned-adm verify
```

## 6. Required Validation

```bash
systemctl is-enabled tuned
systemctl is-active tuned
tuned-adm active
tuned-adm verify
```

The active profile must match the recorded recommendation unless the instructor approved another profile.

## 7. Evidence Students Must Submit

Submit virtualization detection, recommendation, available profiles, active profile before/after, TuneD verification, service state, and reboot proof.

## 8. Rollback or Cleanup

Apply the previous profile recorded in `profile-before.txt`, or disable TuneD:

```bash
tuned-adm off
systemctl disable --now tuned
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
