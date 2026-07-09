# MODULE 07 Cron Notes
> JULY 08, 2026 - WEDNESDAY
---

## 1. What is cron?

`cron` is used to schedule recurring jobs on Linux.

A cron job can run commands or scripts automatically at a specific time, such as:

- Every 15 minutes
- Every day
- Every Monday at 12:15
- Every month

The command used to manage user cron jobs is:

```bash
crontab
```

---

## 2. Cron Time and Date Fields

A cron entry uses five time/date fields followed by the command or script to run.

```text
minute hour day-of-month month day-of-week command
```

| Field | Allowed Values |
|---|---|
| Minute | `0-59` |
| Hour | `0-23` |
| Day of month | `1-31` |
| Month | `1-12` or names |
| Day of week | `0-7` where `0` or `7` is Sunday, or use names |

Example format:

```cron
*/15 * * * * /script.sh
```

Meaning:

```text
Run /script.sh every 15 minutes.
```

---

## 3. Task 1: Run a Script as User harry Every 15 Minutes

### Requirement

As the root user, schedule a script named `/script.sh` to run as user `harry` every 15 minutes.

The script should run this command:

```bash
echo "Harry's job is executed at $(date)" > /harry_cron
```

---

### Step 1: Create the Script

```bash
vim /script.sh
```

Add this line:

```bash
echo "Harry's job is executed at $(date)" > /harry_cron
```

Save and quit:

```vim
:wq
```

---

### Step 2: Give Execute Permission to the Script

```bash
chmod +x /script.sh
```

This allows the script to be executed.

---

### Step 3: Create the Output File and Change Ownership

```bash
touch /harry_cron
chown harry:harry /harry_cron
```

This creates the output file and makes user `harry` the owner.

---

### Step 4: Edit harry's Crontab as root

```bash
crontab -u harry -e
```

Add this cron entry:

```cron
*/15 * * * * /script.sh
```

Save and quit:

```vim
:wq
```

---

### Step 5: Verify harry's Crontab

```bash
crontab -u harry -l
```

Expected entry:

```cron
*/15 * * * * /script.sh
```

---

## 4. Cron Access Control

The root user can restrict which users are allowed to use cron by using these files:

```bash
/etc/cron.allow
/etc/cron.deny
```

---

### Cron Access Rules

| Condition | Result |
|---|---|
| `/etc/cron.allow` exists | User must be listed in this file to use `crontab` |
| `/etc/cron.deny` exists | User must not be listed in this file to use `crontab` |
| Both files exist | `/etc/cron.allow` takes precedence |
| Neither file exists | Only root can use `crontab` |

Important idea:

```text
/etc/cron.allow has higher priority than /etc/cron.deny.
```

---

## 5. Task 2: Allow Only User lara to Use Cron

### Requirement

Schedule a script named `/access.sh` as user `lara`.

The script should run every Monday at **12:15 PM**.

Only user `lara` should be allowed to use cron.

The script should run this command:

```bash
echo "Lara's job is executed at $(date)" > /home/lara/cron_lara
```

---

### Step 1: Allow Only lara to Use Cron

Open `/etc/cron.allow`:

```bash
vim /etc/cron.allow
```

Add this user:

```text
lara
```

Save and quit:

```vim
:wq
```

Because `/etc/cron.allow` exists, only users listed in this file can use `crontab`.

---

### Step 2: Create the Script

```bash
vim /access.sh
```

Add this line:

```bash
echo "Lara's job is executed at $(date)" > /home/lara/cron_lara
```

Save and quit:

```vim
:wq
```

---

### Step 3: Give lara Access to the Script

```bash
setfacl -m u:lara:rx /access.sh
```

This gives user `lara` read and execute permission on `/access.sh`.

---

### Step 4: Switch to User lara

```bash
su - lara
```

---

### Step 5: List lara's Current Crontab

```bash
crontab -l
```

If no cron jobs exist yet, the user may have no crontab.

---

### Step 6: Edit lara's Crontab

```bash
crontab -e
```

Add this cron entry:

```cron
15 12 * * 1 /access.sh
```

Meaning:

```text
Run /access.sh at 12:15 every Monday.
```

Save and quit:

```vim
:wq
```

---

### Step 7: Verify lara's Cron Job

```bash
crontab -l
```

Expected entry:

```cron
15 12 * * 1 /access.sh
```

---

## 6. Useful Cron Commands from the Slides

| Command | Action / Description |
|---|---|
| `crontab -e` | Edit the current user's crontab |
| `crontab -l` | List the current user's crontab |
| `crontab -u harry -e` | As root, edit user harry's crontab |
| `crontab -u harry -l` | As root, list user harry's crontab |
| `vim /etc/cron.allow` | Create or edit the file that allows specific users to use cron |
| `vim /etc/cron.deny` | Create or edit the file that denies specific users from using cron |

---

## 7. Cron Examples

| Cron Entry | Meaning |
|---|---|
| `*/15 * * * * /script.sh` | Run every 15 minutes |
| `15 12 * * 1 /access.sh` | Run every Monday at 12:15 PM |
| `0 0 * * * /script.sh` | Run every day at midnight |
| `0 12 * * * /script.sh` | Run every day at noon |
| `0 8 * * 1-5 /script.sh` | Run Monday through Friday at 8:00 AM |

---

## 8. Quick Student Practice

### Practice 1: Create a Script for Cron

```bash
vim /script.sh
```

Add:

```bash
echo "Cron job executed at $(date)" > /tmp/cron_test
```

Then run:

```bash
chmod +x /script.sh
```

---

### Practice 2: Schedule the Script Every 15 Minutes

```bash
crontab -e
```

Add:

```cron
*/15 * * * * /script.sh
```

Verify:

```bash
crontab -l
```

---

### Practice 3: Read the Cron Entry

Explain this cron entry:

```cron
15 12 * * 1 /access.sh
```

Answer:

```text
Run /access.sh every Monday at 12:15 PM.
```

---

## 9. Exam-Style Summary

| Topic | Must Know |
|---|---|
| Main command | `crontab` |
| Edit current user's cron | `crontab -e` |
| List current user's cron | `crontab -l` |
| Edit another user's cron as root | `crontab -u USER -e` |
| List another user's cron as root | `crontab -u USER -l` |
| Allow file | `/etc/cron.allow` |
| Deny file | `/etc/cron.deny` |
| Every 15 minutes | `*/15 * * * *` |
| Monday at 12:15 PM | `15 12 * * 1` |
