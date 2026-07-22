# NexusVentures Project 09: Reliable Time Synchronization with Chrony

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Configure the VM as an NTP client of the assigned server. The exam example uses `system2.eight.example.com` at `192.168.55.151`.

## 2. Business Scenario

NexusVentures requires accurate time for authentication, logs, incident investigation, and transaction sequencing.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.


## 5. Step-by-Step Solution

### Step 1: Obtain the approved source

```bash
NTP_SERVER="192.168.55.151"
```

The server must provide NTP on UDP 123.

### Step 2: Install and back up

```bash
dnf install -y chrony
mkdir -p /root/nexusventures-project09/{backups,evidence}
cp -a /etc/chrony.conf /root/nexusventures-project09/backups/chrony.conf-before
chronyc tracking > /root/nexusventures-project09/evidence/tracking-before.txt 2>&1 || true
chronyc sources -v > /root/nexusventures-project09/evidence/sources-before.txt 2>&1 || true
```

### Step 3: Add the server

```bash
vi /etc/chrony.conf
```

Add:

```text
server 192.168.55.151 iburst
```

Use the instructor value.

### Step 4: Confirm and validate syntax

```bash
grep -nE '^[[:space:]]*(server|pool)[[:space:]]' /etc/chrony.conf
chronyd -p -f /etc/chrony.conf
```

### Step 5: Enable and restart

```bash
systemctl enable chronyd
systemctl restart chronyd
systemctl status chronyd --no-pager
```

### Step 6: Request measurements

```bash
chronyc online
chronyc burst 4/4
sleep 10
chronyc tracking
chronyc sources -v
chronyc sourcestats -v
timedatectl
```

`^*` is the selected source, `^+` is usable, and `^?` has no valid measurement yet.

### Step 7: Review logs

```bash
journalctl -u chronyd -n 30 --no-pager
```

### Step 8: Reboot and validate

```bash
reboot
```

Then:

```bash
systemctl is-active chronyd
systemctl is-enabled chronyd
chronyc tracking
chronyc sources -v
timedatectl
```

## 6. Required Validation

```bash
chronyd -p -f /etc/chrony.conf >/dev/null
systemctl is-active chronyd
systemctl is-enabled chronyd
grep -E "^[[:space:]]*server[[:space:]]+$NTP_SERVER([[:space:]]|$)" /etc/chrony.conf
chronyc tracking
chronyc sources -v
```

## 7. Evidence Students Must Submit

Submit configuration backup, source directive, syntax validation, service status, tracking and sources output, timedatectl, journal output, and reboot proof.

## 8. Rollback or Cleanup

```bash
cp -a /root/nexusventures-project09/backups/chrony.conf-before /etc/chrony.conf
chronyd -p -f /etc/chrony.conf
systemctl restart chronyd
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
