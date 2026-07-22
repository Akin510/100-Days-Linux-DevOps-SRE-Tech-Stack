# NexusVentures Project 07: Scheduled User Work and Cron Access Control

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create a daily 12:30 cron job for `harry` that prints `hello`, and deny `natasha` permission to create cron jobs.

## 2. Business Scenario

NexusVentures permits scheduled processing only for approved identities and needs evidence that access control is enforced.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 04 must have created Harry and Natasha.

## 5. Step-by-Step Solution

### Step 1: Confirm users and service

```bash
id harry
id natasha
dnf install -y cronie
systemctl enable --now crond
```

### Step 2: Back up existing state

```bash
mkdir -p /root/nexusventures-project07/backups
crontab -u harry -l   > /root/nexusventures-project07/backups/harry-before 2>/dev/null || true
cp -a /etc/cron.deny   /root/nexusventures-project07/backups/cron.deny-before 2>/dev/null || true
```

### Step 3: Add Harry’s job

```bash
{
  crontab -u harry -l 2>/dev/null || true
  echo '30 12 * * * /usr/bin/echo "hello" >> /home/harry/cron-hello.log 2>&1'
} | awk '!seen[$0]++' | crontab -u harry -

crontab -u harry -l
```

The five fields are minute, hour, day of month, month, and day of week.

### Step 4: Deny Natasha

```bash
ls -l /etc/cron.allow /etc/cron.deny 2>/dev/null
touch /etc/cron.deny
grep -qxF natasha /etc/cron.deny || echo natasha >> /etc/cron.deny
chmod 600 /etc/cron.deny
```

If `/etc/cron.allow` exists, it takes precedence and the instructor must define the intended policy.

### Step 5: Test denial

```bash
runuser -l natasha -c 'crontab -l'
runuser -l natasha -c 'crontab -e'
```

A denial is expected.

### Step 6: Optional rapid functional test

```bash
{
  crontab -u harry -l
  echo '* * * * * /usr/bin/date >> /home/harry/cron-minute-test.log 2>&1'
} | awk '!seen[$0]++' | crontab -u harry -

sleep 75
cat /home/harry/cron-minute-test.log
crontab -u harry -l | grep -v 'cron-minute-test.log' | crontab -u harry -
```

### Step 7: Inspect logs and reboot

```bash
journalctl -u crond -n 30 --no-pager
reboot
```

Then:

```bash
systemctl is-active crond
crontab -u harry -l
grep -x natasha /etc/cron.deny
```

## 6. Required Validation

```bash
systemctl is-enabled crond
systemctl is-active crond
crontab -u harry -l | grep '^30 12 '
grep -qxF natasha /etc/cron.deny
! runuser -l natasha -c 'crontab -l'
```

## 7. Evidence Students Must Submit

Submit Harry’s crontab, explanation of the five fields, Natasha’s denial, service status, optional minute-test output, journal evidence, and reboot validation.

## 8. Rollback or Cleanup

Restore Harry’s saved crontab or remove it:

```bash
if [ -s /root/nexusventures-project07/backups/harry-before ]; then
  crontab -u harry /root/nexusventures-project07/backups/harry-before
else
  crontab -u harry -r
fi
```
Restore `/etc/cron.deny` from the backup or remove only Natasha’s line.

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
