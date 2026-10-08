# Exam Question #5: Shared Group Directories and Permissions

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Question #5
On **Node1**, as root, create shared collaboration directories for group-based access with the following requirements[cite: 1]:

1. Create the following directories[cite: 1]:
   * `/groups/admins`[cite: 1]
   * `/groups/users`[cite: 1]

2. Configure `/groups/admins` as follows[cite: 1]:
   * The **group owner** of the directory must be `admins`[cite: 1]
   * Members of the `admins` group must have **full access** (read, write, and execute)[cite: 1]
   * No access must be granted to users outside the admin group[cite: 1]
   * The directory owner must remain `root`, with full access[cite: 1]
   * All newly created files and directories within `/groups/admins` must automatically inherit the `admin` group ownership[cite: 1]

3. Configure `/groups/users` as follows[cite: 1]:
   * The **group owner** must be `users`[cite: 1]
   * Owner and members of the `users` group must have read, write, and execute access[cite: 1]
   * Other users must have no access[cite: 1]
   * New files created in this directory can only be deleted by the file owner or root[cite: 1]

## Exam Question #5 Solution
### Solution Question 1:
```bash
mkdir -p /groups/admins /groups/users
```

## Solution Question 2:
```bash
ls -ld /groups/admins
drwxr-xr-x. 2 root root 6 Oct  8 10:32 /groups/admins

## Let us Change the group "root" using either 'chgrp' or 'chown -R' (NOT chmod - changes file permissions!)
chgrp admins /groups/admins
ls -ld /groups/admins
drwxr-xr-x. 2 root admins 6 Oct  8 10:32 /groups/admins
ls -l /groups
drwxr-xr-x. 2 root root 6 Oct  8 10:32 admins
drwxrwx---. 2 root root 6 Oct  8 10:32 users

chmod 770 /groups/admins
ls -ld /groups/admins
drwxrwx---. 2 root root 6 Oct  8 10:32 /groups/admins
# Full Access Owner=rwx and group=rwx  and others=---

#Apply Group ID
chmod g+s /groups/admins
ls -ld /groups/admins
drwxrws---. 2 root root 6 Oct  8 10:32 /groups/admins
```
### Solution Question 3:
```bash
ls -ld /groups/users
drwxr-xr-x. 2 root root 6 Oct  8 10:32 /groups/users
#Change ownership
chown root:users /groups/users
ls -ld /groups/users
drwxr-xr-x. 2 root users 6 Oct  8 10:32 /groups/users
#Apply Sticky Bit
chmod 1770 /groups/users
ls -ld /groups/users
drwxr-xr--T. 2 root users 6 Oct  8 10:32 /groups/users
```

## Test and Validate
```bash
touch /groups/admins/file1.txt
```