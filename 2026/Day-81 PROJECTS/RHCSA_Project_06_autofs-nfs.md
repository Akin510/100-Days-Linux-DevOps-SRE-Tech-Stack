# NexusVentures Project 06: On-Demand NFS Access with AutoFS

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Automatically mount NFS shares `public` and `private` under `/automount`; public must be read-only, private read-write, and both must unmount after 30 seconds of inactivity.

## 2. Business Scenario

NexusVentures clients need on-demand access to central documentation and a writable workspace without keeping unused network mounts permanently connected.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Two VMs are required. Use the exact server IP and client network assigned by the instructor.

## 5. Step-by-Step Solution

## Server VM

### Step 1: Record assigned values

```bash
NFS_SERVER_IP="192.168.55.151"
CLIENT_NETWORK_CIDR="192.168.55.0/24"
```

Use instructor-assigned values.

### Step 2: Install and create shares

```bash
dnf install -y nfs-utils
mkdir -p /public /private
echo 'NexusVentures public documentation' > /public/readme.txt
echo 'NexusVentures private workspace' > /private/readme.txt
chmod 0755 /public
chmod 0777 /private
```

`0777` satisfies the exam-style “read-write for all” task. Production should use groups and tighter permissions.

### Step 3: Export securely

```bash
mkdir -p /etc/exports.d
cat > /etc/exports.d/nexusventures.exports <<EOF
/public  ${CLIENT_NETWORK_CIDR}(ro,sync,root_squash)
/private ${CLIENT_NETWORK_CIDR}(rw,sync,root_squash)
EOF
exportfs -rav
exportfs -v
```

The sample answer used `no_root_squash`; this project keeps safer `root_squash`.

### Step 4: Firewall and services

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --permanent --add-service=mountd
firewall-cmd --reload
systemctl enable --now nfs-server
systemctl is-active nfs-server
showmount -e localhost
```

## Client VM

### Step 5: Install and prepare

```bash
dnf install -y nfs-utils autofs
mkdir -p /automount
NFS_SERVER_IP="192.168.55.151"
```

### Step 6: Create master map

```bash
mkdir -p /etc/auto.master.d
cat > /etc/auto.master.d/nexusventures.autofs <<'EOF'
/automount /etc/auto.nexusventures --timeout=30
EOF
```

### Step 7: Create indirect map

```bash
cat > /etc/auto.nexusventures <<EOF
public  -fstype=nfs,ro,sync ${NFS_SERVER_IP}:/public
private -fstype=nfs,rw,sync ${NFS_SERVER_IP}:/private
EOF
```

### Step 8: Validate maps and start

```bash
automount -m
systemctl enable --now autofs
systemctl status autofs --no-pager
```

### Step 9: Trigger mounts

```bash
findmnt -t nfs,nfs4
ls -l /automount/public
findmnt /automount/public
ls -l /automount/private
findmnt /automount/private
```

### Step 10: Test access

Public write must fail:

```bash
touch /automount/public/should-fail
```

Private write must succeed:

```bash
touch /automount/private/client-write-test
ls -l /automount/private/client-write-test
```

### Step 11: Test timeout

```bash
cd /
sleep 35
findmnt -t nfs,nfs4
```

### Step 12: Reboot client and trigger again

```bash
reboot
```

After reboot:

```bash
systemctl is-active autofs
ls /automount/public
ls /automount/private
findmnt -t nfs,nfs4
```

## 6. Required Validation

Server:

```bash
exportfs -v
systemctl is-enabled nfs-server
systemctl is-active nfs-server
firewall-cmd --list-services
```

Client:

```bash
automount -m
systemctl is-enabled autofs
systemctl is-active autofs
findmnt /automount/public
findmnt /automount/private
! touch /automount/public/deny-test
touch /automount/private/write-test
```

## 7. Evidence Students Must Submit

Submit server exports, firewall services, NFS status, AutoFS master and map files, mount evidence, failed public write, successful private write, timeout result, and reboot test.

## 8. Rollback or Cleanup

Client:

```bash
systemctl disable --now autofs
rm -f /etc/auto.master.d/nexusventures.autofs /etc/auto.nexusventures
rm -rf /automount
```

Server:

```bash
rm -f /etc/exports.d/nexusventures.exports
exportfs -rav
systemctl disable --now nfs-server
rm -rf /public /private
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
