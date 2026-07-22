# NexusVentures Project 13: Robust Bash File Collection Automation

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Task Converted to a Project

Create an executable script named `mysearch` that finds regular files smaller than 1 MiB under `/usr/share` and copies them under `/root/myfiles`.

## 2. Business Scenario

NexusVentures needs a repeatable evidence collector that safely handles spaces in filenames and duplicate basenames.

## 3. Learning Outcomes

Students will plan the change, record the original state, implement the configuration, explain each command, validate the result, test reboot persistence where applicable, and document rollback.

## 4. Safety and Prerequisites

- Confirm the assigned VM with `hostnamectl` and `ip -brief address`.
- Confirm `whoami` returns `root`.
- Create a Xen Orchestra snapshot before disruptive work.


## 5. Step-by-Step Solution

### Step 1: Check capacity

```bash
df -h /root
```

### Step 2: Create the script

```bash
cat > /root/mysearch <<'EOF'
#!/bin/bash
set -euo pipefail

SOURCE="/usr/share"
DESTINATION="/root/myfiles"

if [[ ! -d "$SOURCE" ]]; then
    echo "ERROR: missing source: $SOURCE" >&2
    exit 1
fi

rm -rf "$DESTINATION"
mkdir -p "$DESTINATION"
count=0

while IFS= read -r -d '' file
do
    relative="${file#/}"
    target="$DESTINATION/$relative"
    mkdir -p "$(dirname "$target")"
    cp --preserve=mode,timestamps "$file" "$target"
    count=$((count + 1))
done < <(find "$SOURCE" -xdev -type f -size -1M -print0)

echo "Copied $count files from $SOURCE to $DESTINATION"
EOF
```

### Step 3: Set permissions and validate syntax

```bash
chown root:root /root/mysearch
chmod 0750 /root/mysearch
bash -n /root/mysearch
```

### Step 4: Count expected files

```bash
EXPECTED=$(find /usr/share -xdev -type f -size -1M | wc -l)
echo "$EXPECTED"
```

### Step 5: Run

```bash
/root/mysearch | tee /root/mysearch-run.txt
```

### Step 6: Compare counts

```bash
ACTUAL=$(find /root/myfiles -type f | wc -l)
printf 'Expected=%s\nActual=%s\n' "$EXPECTED" "$ACTUAL"
test "$EXPECTED" -eq "$ACTUAL"
```

### Step 7: Inspect and verify rule

```bash
find /root/myfiles -type f | head -20
du -sh /root/myfiles
find /root/myfiles -type f ! -size -1M -print
```

The final command should produce no output.

### Step 8: Run a second time

```bash
/root/mysearch
```

The script should rebuild the destination without duplicate growth.

## 6. Required Validation

```bash
bash -n /root/mysearch
test -x /root/mysearch
EXPECTED=$(find /usr/share -xdev -type f -size -1M | wc -l)
ACTUAL=$(find /root/myfiles -type f | wc -l)
test "$EXPECTED" -eq "$ACTUAL"
! find /root/myfiles -type f ! -size -1M | grep -q .
```

## 7. Evidence Students Must Submit

Submit script source, mode, syntax validation, expected and actual counts, run output, sample destination tree, disk usage, and explanation of `set -euo pipefail` and `-print0`.

## 8. Rollback or Cleanup

```bash
rm -f /root/mysearch /root/mysearch-run.txt
rm -rf /root/myfiles
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
