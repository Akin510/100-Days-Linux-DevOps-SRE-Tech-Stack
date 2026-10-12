# EXAM QUESTION #8: Ownership, Permissions, and ACL's
* ACL's are no longer part of RHCSA 10 exam anymore.

## 1. Exam Question #8

On Node1, copy the file /etc/fstab to /var/tmp and configure its ownership and permissions to meet the followign requirements:
`(If the questions says "Copy the file and ensure its original persmissions and timestamps are preserved, "then  -p flag is mandatory, like cp -p or if the word recursive is used then cp -r)`
* Th copied file must be onwed by root. 
* The file must belong to the admins group.
* Teh file must not be executable by any user.
* The user owner and group owner should have read and write access.
* User harry must have read and write access to the file.
* User bruce can read but not write to the file.
* User natasha must have no read or write access to the file.
* All other users, including users created in teh future, must have read-only access to the file.


## Exam Question #8 Solution
## Solution Question 1
```bash
cp -p /etc/fstab /var/tmp/fstab
ls -ld /var/tmp/fstab
-rw-r--r--. 1 root root 666 Oct 12 10:21 /var/tmp/fstab
```

## Solution Question 2 and 3 - copied file must be owned by root and belong to 'admins' group
```bash
ls -ld /var/tmp/fstab
-rw-r--r--. 1 root root 666 Oct 12 10:21 /var/tmp/fstab
chown root:admins /var/tmp/fstab
#Check
ls -ld /var/tmp/fstab
-rw-r--r--. 1 root admins 666 Oct 12 10:21 /var/tmp/fstab
```
## Solution Question 4 
```bash
ls -ld /var/tmp/fstab
-rw-r--r--. 1 root admins 666 Oct 12 10:21 /var/tmp/fstab
chmod -x /var/tmp/fstab     #ugo-x, a-x   -x means we are removing executable permissions
-rw-r--r--. 1 root admins 666 Oct 12 10:21 /var/tmp/fstab
```

## Solution Question 5 and 9
```bash
chmod 664 /var/tmp/fstab
ls -ld /var/tmp/fstab
```
## Solution 6 - User Harry
```bash
setfacl -m u:harry:rw- /var/tmp/fstab
setfacl -m u:brue:r-- /var/tmp/fstab
setfacl -m u:natasha:--- /var/tmp/fstab
getfacl /var/tmp/fstab
```

