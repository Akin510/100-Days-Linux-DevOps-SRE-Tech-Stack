# NexusVentures Project 16: Thin-Provisioned VDO Storage Volume

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create a VDO-backed volume named `vectra` with a logical size of 50 GiB and mount it at `/test`.

## 2. Business Scenario

NexusVentures is evaluating storage deduplication, compression, and thin provisioning for repetitive application data.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.
This erases the selected disk. The blank device and physical allocation must be approved by the instructor.

## 5. Step-by-Step Solution

### Step 1: Install tools

```bash
dnf install -y lvm2 vdo kmod-kvdo xfsprogs
```

This project uses LVM-integrated VDO on Rocky Linux 9. Older material may show a standalone `vdo create` command.

### Step 2: Identify an unused disk

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
VDO_DISK="/dev/sdc"
findmnt -S "$VDO_DISK"
wipefs -n "$VDO_DISK"
```

Use only the device assigned by the instructor.

### Step 3: Snapshot and clear the blank disk

```bash
wipefs -a "$VDO_DISK"
```

### Step 4: Create PV and VG

```bash
pvcreate "$VDO_DISK"
vgcreate vdovg "$VDO_DISK"
pvs
vgs
```

### Step 5: Choose physical and logical sizes

```bash
PHYSICAL_SIZE="5G"
VIRTUAL_SIZE="50G"
vgs vdovg -o vg_size,vg_free
```

The physical size must fit the disk. The virtual size is the logical capacity presented to the filesystem.

### Step 6: Create the VDO LV

```bash
lvcreate --type vdo   --name vectra   --size "$PHYSICAL_SIZE"   --virtualsize "$VIRTUAL_SIZE"   vdovg
```

### Step 7: Inspect topology

```bash
lvs -a -o lv_name,vg_name,lv_attr,lv_size,pool_lv,data_percent,metadata_percent
```

### Step 8: Format and mount

```bash
mkfs.xfs /dev/vdovg/vectra
mkdir -p /test
VDO_UUID=$(blkid -s UUID -o value /dev/vdovg/vectra)
cp -a /etc/fstab /root/fstab-before-project16
echo "UUID=$VDO_UUID /test xfs defaults 0 0" >> /etc/fstab
mount -a
```

### Step 9: Validate logical size

```bash
findmnt /test
df -hT /test
lvs -a -o lv_name,lv_size,data_percent,metadata_percent vdovg
```

### Step 10: Write compressible sample data

```bash
dd if=/dev/zero of=/test/zero-data.bin bs=1M count=500 status=progress
sync
du -h /test/zero-data.bin
df -h /test
lvs -a -o lv_name,lv_size,data_percent,metadata_percent vdovg
vdostats --human-readable 2>/dev/null || true
```

### Step 11: Reboot and validate

```bash
reboot
```

Then:

```bash
findmnt /test
df -hT /test
lvs -a vdovg
test -f /test/zero-data.bin
mount -a
```

## 6. Required Validation

```bash
lvs vdovg/vectra
findmnt /test
test "$(findmnt -n -o FSTYPE /test)" = "xfs"
df -h /test
mount -a
```

The logical filesystem should be close to 50 GiB even though the physical allocation is smaller.

## 7. Evidence Students Must Submit

Submit assigned disk, package list, PV/VG/LV output, physical and virtual sizes, VDO topology, XFS and fstab evidence, mount output, statistics, sample-data test, and reboot proof.

## 8. Rollback or Cleanup

```bash
umount /test
```

Remove the project fstab line, then:

```bash
lvremove -y /dev/vdovg/vectra
vgremove -y vdovg
pvremove -y "$VDO_DISK"
wipefs -a "$VDO_DISK"
rmdir /test
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
