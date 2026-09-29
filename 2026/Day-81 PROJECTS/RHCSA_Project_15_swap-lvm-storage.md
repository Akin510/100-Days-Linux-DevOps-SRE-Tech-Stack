# NexusVentures Project 15: Swap and LVM Database Storage

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Using an instructor-provided blank disk, create a 512 MiB swap partition and an LVM LV named `database` in VG `datastore`. Use 8 MiB extents, allocate 50 extents, format ext3, and mount at `/mnt/database`.

## 2. Business Scenario

NexusVentures is adding emergency memory capacity and a dedicated database filesystem. The work must be persistent and data-safe.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
The selected disk will be erased. Instructor approval and a Xen Orchestra snapshot are mandatory.

## 5. Step-by-Step Solution

### Step 1: Identify the assigned blank disk

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
blkid
LAB_DISK="/dev/sdb"
findmnt -S "$LAB_DISK"
wipefs -n "$LAB_DISK"
```

Never assume `/dev/sdb`; use the instructor-assigned device.

### Step 2: Save metadata and create a snapshot

```bash
mkdir -p /root/nexusventures-project15/evidence
sfdisk -d "$LAB_DISK"   > /root/nexusventures-project15/evidence/partition-table-before.txt 2>/dev/null || true
cp -a /etc/fstab /root/nexusventures-project15/fstab-before
```

### Step 3: Create GPT partitions

**This erases the selected disk.**

```bash
parted -s "$LAB_DISK" mklabel gpt
parted -s "$LAB_DISK" mkpart swap linux-swap 1MiB 513MiB
parted -s "$LAB_DISK" set 1 swap on
parted -s "$LAB_DISK" mkpart lvm 513MiB 100%
parted -s "$LAB_DISK" set 2 lvm on
partprobe "$LAB_DISK"
udevadm settle
lsblk "$LAB_DISK"
```

For `/dev/sdb`:

```bash
SWAP_PART="/dev/sdb1"
LVM_PART="/dev/sdb2"
```

## Part A: Swap

### Step 4: Initialize and persist swap

```bash
mkswap "$SWAP_PART"
SWAP_UUID=$(blkid -s UUID -o value "$SWAP_PART")
echo "UUID=$SWAP_UUID none swap defaults 0 0" >> /etc/fstab
swapon -a
swapon --show
free -h
```

## Part B: LVM

### Step 5: Create PV and VG

```bash
pvcreate "$LVM_PART"
vgcreate -s 8M datastore "$LVM_PART"
pvs
vgs -o vg_name,vg_size,vg_free,vg_extent_size
```

### Step 6: Create 50 extents

```bash
lvcreate -l 50 -n database datastore
lvs -o lv_name,vg_name,lv_size,devices
```

50 × 8 MiB is approximately 400 MiB.

### Step 7: Create and mount ext3

```bash
dnf install -y e2fsprogs
mkfs.ext3 /dev/datastore/database
mkdir -p /mnt/database
DB_UUID=$(blkid -s UUID -o value /dev/datastore/database)
echo "UUID=$DB_UUID /mnt/database ext3 defaults 0 0" >> /etc/fstab
mount -a
```

### Step 8: Test

```bash
findmnt /mnt/database
df -hT /mnt/database
lsblk -f
swapon --show
echo 'NexusVentures database test' > /mnt/database/validation.txt
sync
```

### Step 9: Reboot and validate

```bash
reboot
```

Then:

```bash
swapon --show
findmnt /mnt/database
cat /mnt/database/validation.txt
vgs -o vg_name,vg_extent_size
lvs datastore
mount -a
```

## 6. Required Validation

```bash
swapon --show | grep -F "$SWAP_PART"
vgs datastore -o vg_extent_size
lvs /dev/datastore/database
findmnt /mnt/database
test -f /mnt/database/validation.txt
mount -a
```

## 7. Evidence Students Must Submit

Submit instructor disk assignment, `lsblk` before and after, partition table, swap UUID and activation, fstab entries, PV/VG/LV output, 8 MiB extent size, 50-extents result, filesystem and mount output, and reboot proof.

## 8. Rollback or Cleanup

Unmount and remove in reverse order:

```bash
umount /mnt/database
swapoff "$SWAP_PART"
```

Remove the two project lines from `/etc/fstab`, then:

```bash
lvremove -y /dev/datastore/database
vgremove -y datastore
pvremove -y "$LVM_PART"
wipefs -a "$SWAP_PART" "$LVM_PART"
```

Use snapshot reversion for full rollback.

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
