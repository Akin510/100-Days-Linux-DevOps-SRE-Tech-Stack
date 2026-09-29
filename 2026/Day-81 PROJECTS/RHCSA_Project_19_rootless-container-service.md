# NexusVentures Project 19: Rootless Rsyslog Container as a Startup Service

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create a rootless container named `logserver` for user `paradise` from an approved rsyslog image and configure it to start automatically as a user systemd service.

## 2. Business Scenario

NexusVentures is evaluating rootless containers and user-level service management for an isolated logging workload.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Use a direct Paradise login for user-systemd commands. Project 20 depends on this project.

## 5. Step-by-Step Solution

## Root preparation

### Step 1: Install container tools

```bash
dnf install -y container-tools
podman --version
```

### Step 2: Create the user if needed

```bash
id paradise >/dev/null 2>&1 || useradd -m paradise
passwd paradise
```

### Step 3: Enable lingering

```bash
loginctl enable-linger paradise
loginctl show-user paradise -p Linger
```

Expected: `Linger=yes`.

## Direct Paradise session

Open a new SSH or console login directly as `paradise`. Do not rely on `su` for the user-systemd tests.

### Step 4: Confirm rootless context

```bash
whoami
id
podman info --format '{{.Host.Security.Rootless}}'
```

Expected: user `paradise`, rootless `true`.

### Step 5: Pull the approved rsyslog image

```bash
CONTAINER_IMAGE="docker.io/lendingworks/rsyslog"
podman pull "$CONTAINER_IMAGE"
podman images
```

If this image is unavailable, use the instructor-approved replacement.

### Step 6: Create a Quadlet definition

```bash
mkdir -p ~/.config/containers/systemd
cat > ~/.config/containers/systemd/logserver.container <<EOF
[Unit]
Description=NexusVentures Rootless Rsyslog Container

[Container]
Image=${CONTAINER_IMAGE}
ContainerName=logserver

[Service]
Restart=always
TimeoutStartSec=180

[Install]
WantedBy=default.target
EOF
```

### Step 7: Generate and start the user unit

```bash
systemctl --user daemon-reload
systemctl --user start logserver.service
systemctl --user status logserver.service --no-pager
```

### Step 8: Inspect

```bash
podman ps -a
podman inspect logserver   --format 'Name={{.Name}} Image={{.ImageName}} Status={{.State.Status}}'
```

If the container exits:

```bash
journalctl --user -u logserver.service -n 50 --no-pager
podman logs logserver
```

The instructor may need to provide image-specific arguments.

### Step 9: Reboot host

As root:

```bash
reboot
```

After reboot, log in directly as Paradise:

```bash
loginctl show-user paradise -p Linger
systemctl --user status logserver.service --no-pager
podman ps -a
```

## 6. Required Validation

Root:

```bash
loginctl show-user paradise -p Linger | grep 'Linger=yes'
```

Paradise direct session:

```bash
podman container exists logserver
systemctl --user status logserver.service --no-pager
podman inspect logserver --format '{{.State.Status}}'
```

The unit and container must return after reboot.

## 7. Evidence Students Must Submit

Submit Podman version, Paradise identity, rootless status, image listing, Quadlet file, linger state, user-service status, container inspection, troubleshooting logs if used, and reboot proof.

## 8. Rollback or Cleanup

As Paradise:

```bash
systemctl --user stop logserver.service
rm -f ~/.config/containers/systemd/logserver.container
systemctl --user daemon-reload
podman rm -f logserver 2>/dev/null || true
```

As root:

```bash
loginctl disable-linger paradise
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
