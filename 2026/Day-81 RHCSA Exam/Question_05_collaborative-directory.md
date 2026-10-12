# Exam Question #5: Shared Group Directories and Permissions

> **Platform:** Rocky Linux 9 VM in Xen Orchestra  
> **Account:** `root`  
> **Standard:** Keep SELinux enforcing and firewalld enabled. Persistent work must survive reboot.

## 1. Exam Question #5
On **Node1**, as root, create shared collaboration directories for group-based access with the following requirements:

1. Create the following directories:
   * `/groups/admins`
   * `/groups/users`

2. Configure `/groups/admins` as follows:
   * The **group owner** of the directory must be `admins`
   * Members of the `admins` group must have **full access** (read, write, and execute)
   * No access must be granted to users outside the admin group
   * The directory owner must remain `root`, with full access
   * All newly created files and directories within `/groups/admins` must automatically inherit the `admin` group ownership

3. Configure `/groups/users` as follows:
   * The **group owner** must be `users`
   * Owner and members of the `users` group must have read, write, and execute access
   * Other users must have no access[cite: 1]
   * New files created in this directory can only be deleted by the file owner or root

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
`Why this happens on the exam:`
* Lowercase t means: Sticky bit is on AND others have execute permission (--x).
* Capital T means: Sticky bit is on AND others do not have execute permission (--- or r--).

`IMPORTANT` - If the exam says sticky bit is on AND others should have execute permission. Then, to correct the capital T to a lowercase t, you need to add the execute permission (+x) for others on the /groups/users directory
```bash
chmod o+x /groups/users
ls -ld /groups/users
```

> Why is there a capital T?
* The t flag represents the Sticky Bit (used on shared directories like /tmp so users can only delete their own files).
* `Capital T`: The Sticky Bit is set, but others do not have execute (x) permissions.
* `Lowercase t`: The Sticky Bit is set, and others DO have execute (x) permissions.
In Linux permissions, executable search permission (x) on a directory is required to enter (cd) or access files inside it. Without x for others, the sticky bit is effectively dormant, which Linux indicates using a capital T.
## Test and Validate
```bash
touch /groups/admins/file1.txt
```