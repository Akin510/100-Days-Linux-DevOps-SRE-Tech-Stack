# NexusVentures Project 01: Network Foundation and Persistent Identity

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Configure the hostname, static IPv4 address, gateway, and DNS server. The sample paper uses `system1.eight.example.com`, `192.168.55.150/24`, gateway `192.168.55.1`, and DNS `8.8.8.8`.

## 2. Business Scenario

NexusVentures is commissioning a Linux server. Stable network identity is required before repositories and services can be deployed. Because all students share a Xen Orchestra network, every VM must receive a **unique instructor-assigned IP address**.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project01/`.
- Use only a unique IP allocated by the instructor.
- Never perform the activation step without console access.

## 5. Step-by-Step Solution

### Step 1: Work from the Xen Orchestra console

A network change can disconnect SSH. Keep the console open.

### Step 2: Identify the interface and connection

```bash
mkdir -p /root/nexusventures-project01/evidence
nmcli device status
nmcli connection show
ip -brief address
ip route

IFACE=$(ip route show default | awk '{print $5; exit}')
CONNECTION=$(nmcli -g GENERAL.CONNECTION device show "$IFACE")
printf 'Interface=%s\nConnection=%s\n' "$IFACE" "$CONNECTION"
```

Do not continue if either variable is empty.

### Step 3: Record the original state

```bash
hostnamectl > /root/nexusventures-project01/evidence/hostname-before.txt
ip -brief address > /root/nexusventures-project01/evidence/address-before.txt
ip route > /root/nexusventures-project01/evidence/routes-before.txt
nmcli connection show "$CONNECTION"   > /root/nexusventures-project01/evidence/connection-before.txt
cat /etc/resolv.conf   > /root/nexusventures-project01/evidence/resolv-before.txt
```

### Step 4: Enter instructor-assigned values

```bash
NEW_HOSTNAME="system1.eight.example.com"
NEW_IP_CIDR="192.168.55.150/24"
NEW_GATEWAY="192.168.55.1"
NEW_DNS="8.8.8.8"
NEW_IP="${NEW_IP_CIDR%/*}"
```

The values above are examples. Never give multiple students the same IP.

### Step 5: Check for a duplicate address

```bash
dnf install -y iputils
arping -D -I "$IFACE" "$NEW_IP" -c 3
```

Stop if another host responds.

### Step 6: Set the hostname

```bash
hostnamectl set-hostname "$NEW_HOSTNAME"
hostnamectl
```

### Step 7: Save the static profile

```bash
nmcli connection modify "$CONNECTION"   ipv4.method manual   ipv4.addresses "$NEW_IP_CIDR"   ipv4.gateway "$NEW_GATEWAY"   ipv4.dns "$NEW_DNS"   ipv4.never-default no

nmcli -f connection.id,connection.interface-name,ipv4.method,ipv4.addresses,ipv4.gateway,ipv4.dns   connection show "$CONNECTION"
```

### Step 8: Activate from the console

```bash
nmcli connection up "$CONNECTION"
```

### Step 9: Validate in layers

```bash
hostnamectl --static
ip -4 address show "$IFACE"
ip route
nmcli device show "$IFACE" | grep -E 'IP4.ADDRESS|IP4.GATEWAY|IP4.DNS'
cat /etc/resolv.conf
ping -c 3 "$NEW_GATEWAY"
ping -c 3 8.8.8.8
getent hosts example.com
curl -I --max-time 10 https://example.com
```

### Step 10: Reboot and retest

```bash
reboot
```

After reboot:

```bash
hostnamectl --static
ip -4 address show "$IFACE"
ip route
nmcli device show "$IFACE" | grep -E 'IP4.ADDRESS|IP4.GATEWAY|IP4.DNS'
getent hosts example.com
systemctl is-active NetworkManager
getenforce
systemctl is-active firewalld
```

## 6. Required Validation

All of these must pass:

```bash
test "$(hostnamectl --static)" = "$NEW_HOSTNAME"
ip -4 address show "$IFACE" | grep -F "$NEW_IP_CIDR"
ip route | grep -F "default via $NEW_GATEWAY"
nmcli -g IP4.DNS device show "$IFACE" | grep -F "$NEW_DNS"
getent hosts example.com
```

## 7. Evidence Students Must Submit

Submit hostname and network evidence before and after, interface and connection names, duplicate-IP test, gateway test, DNS test, and post-reboot output. Explain why duplicate IPs disrupt a shared lab.

## 8. Rollback or Cleanup

Restore the exact original values recorded in `connection-before.txt`. For an original DHCP profile:

```bash
nmcli connection modify "$CONNECTION"   ipv4.method auto ipv4.addresses "" ipv4.gateway "" ipv4.dns ""
nmcli connection up "$CONNECTION"
hostnamectl set-hostname OLD_HOSTNAME
```

Use the Xen Orchestra snapshot if network access cannot be restored.

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
