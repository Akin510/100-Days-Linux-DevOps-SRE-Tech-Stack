# NexusVentures Project 20: Persistent Journal Storage for the Logserver Container

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Configure persistent host journal storage, place journal files in a Paradise-owned directory, and mount that directory into `logserver` at `/var/log/journal`.

## 2. Business Scenario

NexusVentures wants the rootless logging container to inspect persistent journal evidence while keeping the host’s live journal protected.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 19 must be complete. Use a direct Paradise login for user-service commands.

## 5. Step-by-Step Solution

## Root configuration

### Step 1: Back up journald configuration

```bash
mkdir -p /root/nexusventures-project20/backups
cp -a /etc/systemd/journald.conf   /root/nexusventures-project20/backups/journald.conf-before
```

### Step 2: Configure persistent storage with a drop-in

```bash
mkdir -p /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/10-nexus-persistent.conf <<'EOF'
[Journal]
Storage=persistent
EOF
systemd-analyze cat-config systemd/journald.conf
```

### Step 3: Create and label persistent journal directory

```bash
mkdir -p /var/log/journal
systemd-tmpfiles --create --prefix /var/log/journal
restorecon -Rv /var/log/journal
systemctl restart systemd-journald
journalctl --flush
```

### Step 4: Confirm host journal files

```bash
find /var/log/journal -type f -name '*.journal' -ls
journalctl --disk-usage
```

### Step 5: Prepare a copied evidence directory

```bash
mkdir -p /home/paradise/container_journal
cp -a /var/log/journal/. /home/paradise/container_journal/
chown -R paradise:paradise /home/paradise/container_journal
restorecon -Rv /home/paradise/container_journal
find /home/paradise/container_journal -maxdepth 3   -printf '%M %u:%g %p\n' | head -30
```

The project copies evidence instead of exposing the live host journal read-write.

## Paradise direct session

### Step 6: Back up the Quadlet

```bash
cp -a ~/.config/containers/systemd/logserver.container   ~/.config/containers/systemd/logserver.container.before-project20
```

### Step 7: Add the bind mount

Edit:

```bash
vi ~/.config/containers/systemd/logserver.container
```

Under `[Container]`, add:

```text
Volume=/home/paradise/container_journal:/var/log/journal:ro,Z
```

`ro` prevents writes. `Z` applies a private SELinux container label.

### Step 8: Reload and restart

```bash
systemctl --user daemon-reload
systemctl --user restart logserver.service
systemctl --user status logserver.service --no-pager
```

### Step 9: Inspect mount metadata

```bash
podman inspect logserver   --format '{{range .Mounts}}{{.Source}} -> {{.Destination}} ({{.Options}}){{println}}{{end}}'
```

### Step 10: Confirm visibility inside container

```bash
podman exec logserver   sh -c 'find /var/log/journal -type f -name "*.journal" -ls | head'
```

If the image has no shell, submit mount-inspection evidence and use an instructor-approved diagnostic image.

### Step 11: Validate SELinux and services

```bash
getenforce
systemctl is-active systemd-journald
ausearch -m AVC -ts recent | tail -20
```

No unresolved AVC should prevent access.

### Step 12: Reboot validation

```bash
reboot
```

After reboot, as Paradise:

```bash
systemctl --user status logserver.service --no-pager
podman ps -a
podman inspect logserver   --format '{{range .Mounts}}{{.Source}} -> {{.Destination}}{{println}}{{end}}'
podman exec logserver   sh -c 'find /var/log/journal -type f -name "*.journal" | head'
```

## 6. Required Validation

Root:

```bash
systemctl is-active systemd-journald
find /var/log/journal -type f -name '*.journal' | grep -q .
test -d /home/paradise/container_journal
getenforce | grep Enforcing
```

Paradise:

```bash
systemctl --user status logserver.service --no-pager
podman inspect logserver | grep -F '/var/log/journal'
podman exec logserver test -d /var/log/journal
```

## 7. Evidence Students Must Submit

Submit journald drop-in and effective configuration, host persistent journal listing, disk use, copied-directory ownership, Quadlet volume line, container mount inspection, inside-container listing, SELinux status, service status, and reboot proof.

## 8. Rollback or Cleanup

As Paradise:

```bash
cp -a ~/.config/containers/systemd/logserver.container.before-project20   ~/.config/containers/systemd/logserver.container
systemctl --user daemon-reload
systemctl --user restart logserver.service
```

As root:

```bash
rm -f /etc/systemd/journald.conf.d/10-nexus-persistent.conf
systemctl restart systemd-journald
rm -rf /home/paradise/container_journal
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
