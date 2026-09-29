# NexusVentures Project 17: Online LVM and Filesystem Expansion

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Increase the `database` logical volume mounted at `/mnt/database` by **100 additional extents**.

## 2. Business Scenario

NexusVentures database storage needs controlled online growth without losing existing data.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
Project 15 must be complete, and the VG must have at least 100 free extents.

## 5. Step-by-Step Solution

### Step 1: Confirm Project 15 resources

```bash
findmnt /mnt/database
lvs /dev/datastore/database
vgs datastore
```

### Step 2: Record the baseline

```bash
mkdir -p /root/nexusventures-project17/evidence
lvs -o lv_name,vg_name,lv_size,lv_attr   > /root/nexusventures-project17/evidence/lvs-before.txt
vgs -o vg_name,vg_extent_size,vg_free_count,vg_free   > /root/nexusventures-project17/evidence/vgs-before.txt
df -hT /mnt/database   > /root/nexusventures-project17/evidence/df-before.txt
sha256sum /mnt/database/validation.txt   > /root/nexusventures-project17/evidence/validation-before.sha256
```

### Step 3: Check free extents

```bash
vgs datastore -o vg_free_count,vg_extent_size
```

At least 100 free extents are required. With 8 MiB extents, the increase is approximately 800 MiB.

### Step 4: Extend by 100 extents

```bash
lvextend -l +100 -r /dev/datastore/database
```

The plus sign is essential. `-l 100` requests a total of 100 extents; `-l +100` adds 100. `-r` grows the filesystem after the LV.

### Step 5: Validate size and mount

```bash
lvs -o lv_name,vg_name,lv_size /dev/datastore/database
df -hT /mnt/database
findmnt /mnt/database
```

### Step 6: Prove data integrity

```bash
sha256sum -c /root/nexusventures-project17/evidence/validation-before.sha256
cat /mnt/database/validation.txt
```

### Step 7: Save after-state

```bash
lvs -o lv_name,vg_name,lv_size,lv_attr   > /root/nexusventures-project17/evidence/lvs-after.txt
vgs -o vg_name,vg_extent_size,vg_free_count,vg_free   > /root/nexusventures-project17/evidence/vgs-after.txt
df -hT /mnt/database   > /root/nexusventures-project17/evidence/df-after.txt
```

### Step 8: Reboot and validate

```bash
reboot
```

Then:

```bash
findmnt /mnt/database
df -hT /mnt/database
cat /mnt/database/validation.txt
lvs /dev/datastore/database
```

## 6. Required Validation

The LV should grow from 50 extents to 150 extents:

```bash
lvs /dev/datastore/database
findmnt /mnt/database
sha256sum -c /root/nexusventures-project17/evidence/validation-before.sha256
mount -a
```

## 7. Evidence Students Must Submit

Submit free extents before, exact `lvextend` command, LV and filesystem size before/after, checksum proof, mount evidence, and post-reboot validation.

## 8. Rollback or Cleanup

Shrinking is more dangerous than growing. Use the pre-change snapshot for classroom rollback. Do not run `lvreduce` without an approved offline filesystem-shrink plan.

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
