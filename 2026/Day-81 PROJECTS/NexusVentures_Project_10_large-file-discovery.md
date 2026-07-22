# NexusVentures Project 10: Large Configuration File Discovery and Collection

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Find all regular files larger than 4 MiB under `/etc` and copy them to `/find/largefiles`.

## 2. Business Scenario

NexusVentures is investigating unexpected growth under `/etc`. Students must preserve matching files without basename collisions.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.


## 5. Step-by-Step Solution

### Step 1: Prepare and inventory

```bash
mkdir -p /find/largefiles
find /etc -xdev -type f -size +4M -printf '%s %p\n' | sort -n   | tee /root/project10-source-inventory.txt
```

### Step 2: Controlled test file when no matches exist

Only with instructor approval:

```bash
dd if=/dev/zero of=/etc/nexusventures-large-test.bin bs=1M count=5 status=progress
```

Run the inventory again.

### Step 3: Copy while preserving paths

```bash
cd /
find etc -xdev -type f -size +4M   -exec cp --parents --preserve=mode,timestamps -t /find/largefiles {} +
```

Preserving parent paths prevents two files with the same basename from overwriting each other.

### Step 4: Inspect destination

```bash
find /find/largefiles -type f -printf '%s %p\n' | sort -n
du -sh /find/largefiles
```

### Step 5: Compare counts

```bash
SOURCE_COUNT=$(find /etc -xdev -type f -size +4M | wc -l)
DEST_COUNT=$(find /find/largefiles -type f | wc -l)
printf 'Source=%s\nDestination=%s\n' "$SOURCE_COUNT" "$DEST_COUNT"
test "$SOURCE_COUNT" -eq "$DEST_COUNT"
```

### Step 6: Compare checksums

```bash
cd /
while IFS= read -r source; do
  relative="${source#/}"
  destination="/find/largefiles/$relative"
  sha256sum "$source" "$destination"
done < <(find /etc -xdev -type f -size +4M)
```

### Step 7: Save destination inventory

```bash
find /find/largefiles -type f -exec sha256sum {} +   > /root/project10-destination-sha256.txt
```

## 6. Required Validation

```bash
test -d /find/largefiles
SOURCE_COUNT=$(find /etc -xdev -type f -size +4M | wc -l)
DEST_COUNT=$(find /find/largefiles -type f | wc -l)
test "$SOURCE_COUNT" -eq "$DEST_COUNT"
find /find/largefiles -type f -size +4M | grep -q .
```

## 7. Evidence Students Must Submit

Submit source and destination inventories, counts, disk use, checksum comparisons, and explanation of `-xdev`, `+4M`, and `--parents`.

## 8. Rollback or Cleanup

```bash
rm -rf /find/largefiles
rmdir /find 2>/dev/null || true
rm -f /etc/nexusventures-large-test.bin
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
