# NexusVentures Project 03: Secure Apache Service on TCP Port 82

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Make web content in `/var/www/html` accessible through Apache on port 82 while SELinux and firewalld remain enabled.

## 2. Business Scenario

NexusVentures needs an internal application endpoint on a nonstandard port. Apache, SELinux, and firewalld must all agree on the design.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project03/`.
Project 02 should already have installed Apache.

## 5. Step-by-Step Solution

### Step 1: Install required packages

```bash
dnf install -y httpd policycoreutils-python-utils
mkdir -p /root/nexusventures-project03/evidence
```

### Step 2: Preserve existing content

```bash
find /var/www/html -maxdepth 2 -printf '%M %u:%g %p\n'   > /root/nexusventures-project03/evidence/content-before.txt
```

Do not delete instructor-provided files. If empty:

```bash
echo 'NexusVentures service on port 82' > /var/www/html/index.html
```

### Step 3: Create a dedicated Apache drop-in

```bash
SERVER_NAME=$(hostname -f)
cat > /etc/httpd/conf.d/nexusventures-82.conf <<EOF
Listen 82
<VirtualHost *:82>
    ServerName ${SERVER_NAME}
    DocumentRoot /var/www/html
    <Directory /var/www/html>
        Require all granted
    </Directory>
    ErrorLog logs/nexus82_error.log
    CustomLog logs/nexus82_access.log combined
</VirtualHost>
EOF
```

### Step 4: Validate syntax

```bash
httpd -t
```

Expected: `Syntax OK`.

### Step 5: Configure SELinux port type

```bash
getenforce
semanage port -l | grep '^http_port_t'
```

If TCP 82 is not listed:

```bash
semanage port -a -t http_port_t -p tcp 82
```

If 82 already has another SELinux type, review before using `-m`.

```bash
restorecon -Rv /var/www/html
ls -Zd /var/www/html
```

### Step 6: Configure firewalld

```bash
ZONE=$(firewall-cmd --get-default-zone)
firewall-cmd --permanent --zone="$ZONE" --add-port=82/tcp
firewall-cmd --reload
firewall-cmd --zone="$ZONE" --query-port=82/tcp
```

### Step 7: Start and enable Apache

```bash
systemctl enable --now httpd
systemctl status httpd --no-pager
ss -ltnp | grep ':82'
```

### Step 8: Test content

```bash
SERVER_IP=$(hostname -I | awk '{print $1}')
curl --fail http://127.0.0.1:82/
curl --fail "http://${SERVER_IP}:82/"
tail -n 20 /var/log/httpd/nexus82_access.log
```

### Step 9: Reboot validation

```bash
reboot
```

Then:

```bash
httpd -t
systemctl is-active httpd
ss -ltn | grep ':82'
firewall-cmd --query-port=82/tcp
semanage port -l | grep '^http_port_t' | grep 82
curl --fail http://127.0.0.1:82/
getenforce
```

## 6. Required Validation

```bash
httpd -t
systemctl is-enabled httpd
systemctl is-active httpd
firewall-cmd --query-port=82/tcp
semanage port -l | grep '^http_port_t' | grep 82
curl --fail http://127.0.0.1:82/
getenforce | grep Enforcing
```

## 7. Evidence Students Must Submit

Submit Apache drop-in, syntax test, SELinux port mapping, web contexts, firewall rule, listener output, curl result, access log, and post-reboot test.

## 8. Rollback or Cleanup

```bash
systemctl disable --now httpd
rm -f /etc/httpd/conf.d/nexusventures-82.conf
firewall-cmd --permanent --zone="$ZONE" --remove-port=82/tcp
firewall-cmd --reload
```
Remove the SELinux port mapping only if this project created it and no other service needs it.

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
