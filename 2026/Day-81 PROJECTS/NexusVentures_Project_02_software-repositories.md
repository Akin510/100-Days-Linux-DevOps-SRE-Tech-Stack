# NexusVentures Project 02: Controlled Software Repository Configuration

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Configure BaseOS and AppStream repositories. The sample URLs are `http://repo.eight.example.com/BaseOS` and `http://repo.eight.example.com/AppStream`.

## 2. Business Scenario

NexusVentures installs software only from approved repositories. Students will define repository metadata, verify package availability, and install Apache for the next project.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm the account with `whoami`; expected output is `root`.
- Create a Xen Orchestra snapshot before disruptive work.
- Save pre-change evidence under `/root/nexusventures-project02/`.


## 5. Step-by-Step Solution

### Step 1: Obtain reachable URLs

```bash
BASEOS_URL="http://repo.eight.example.com/BaseOS"
APPSTREAM_URL="http://repo.eight.example.com/AppStream"
```

Use instructor-provided URLs. The sample names work only when the lab provides matching DNS and web content.

### Step 2: Record current repositories

```bash
mkdir -p /root/nexusventures-project02/evidence
dnf repolist all > /root/nexusventures-project02/evidence/repolist-before.txt
```

### Step 3: Test the locations

```bash
curl -I --max-time 10 "$BASEOS_URL/"
curl -I --max-time 10 "$APPSTREAM_URL/"
```

### Step 4: Back up and create the repository file

```bash
[ ! -f /etc/yum.repos.d/nexusventures.repo ] ||   cp -a /etc/yum.repos.d/nexusventures.repo   /root/nexusventures-project02/nexusventures.repo.before

cat > /etc/yum.repos.d/nexusventures.repo <<EOF
[nexus-baseos]
name=NexusVentures BaseOS
baseurl=${BASEOS_URL}
enabled=1
gpgcheck=0

[nexus-appstream]
name=NexusVentures AppStream
baseurl=${APPSTREAM_URL}
enabled=1
gpgcheck=0
EOF
```

`gpgcheck=0` matches the isolated exam-style lab. Production repositories should use trusted signatures and keys.

### Step 5: Refresh only these repositories

```bash
dnf clean all
dnf makecache --disablerepo='*'   --enablerepo=nexus-baseos,nexus-appstream

dnf repolist --disablerepo='*'   --enablerepo=nexus-baseos,nexus-appstream
```

### Step 6: Confirm and install Apache

```bash
dnf info httpd --disablerepo='*'   --enablerepo=nexus-baseos,nexus-appstream

dnf install -y httpd --disablerepo='*'   --enablerepo=nexus-baseos,nexus-appstream
rpm -q httpd
```

Do not start Apache until Project 03.

## 6. Required Validation

```bash
dnf makecache --disablerepo='*' --enablerepo=nexus-baseos,nexus-appstream
dnf repolist --disablerepo='*' --enablerepo=nexus-baseos,nexus-appstream
rpm -q httpd
```

## 7. Evidence Students Must Submit

Submit the `.repo` file, URL tests, `dnf repolist`, metadata refresh, and Apache package version. Explain repository IDs, `enabled`, `baseurl`, and `gpgcheck`.

## 8. Rollback or Cleanup

```bash
rm -f /etc/yum.repos.d/nexusventures.repo
dnf clean all
```
Restore the backed-up file if one existed.

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
