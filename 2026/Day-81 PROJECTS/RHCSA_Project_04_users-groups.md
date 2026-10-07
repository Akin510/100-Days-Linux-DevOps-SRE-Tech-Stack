# Exam Question #4 - Identity and Administrative Group Provisioning

## The Exam Question:
On Note1, perform the following user and group management tasks:
1. Create a group named admins with a fixed GID of 3500
2. Create a group named users
3. Create the following user accounts with the specified requirements:
```bash
harry
    - Primary group `admins`
    - Secondary group `users`
    - User ID 3455
natasha
    - Supplementary groups: `admins` and `users`
    - User ID of 3456
sarah
    - Must not be a member of the `admins` group
    - Must not hav access to an interactive shell
bruce
    - Member of `admins` group
    - Home direcctory must be created explicitly
```
4. Set the password for all created users to: `password`


# Exam Solution
## 1. Create a group named admins with a fixed GID of 3500
```bash
#Use help feature so that you are not confused. Option we will select will be "-g"
[root@RedhatExam ~]# groupadd -h
Usage: groupadd [options] GROUP

Options:
  -f, --force                   exit successfully if the group already exists,
                                and cancel -g if the GID is already used
  -g, --gid GID                 use GID for the new group
  -h, --help                    display this help message and exit
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -o, --non-unique              allow to create groups with duplicate
                                (non-unique) GID
  -p, --password PASSWORD       use this encrypted password for the new group
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
  -P, --prefix PREFIX_DI        directory prefix
  -U, --users USERS             list of user members of this group

```
Let us now create the group using '-g' flag
```bash
groupadd -g 3500 admins
```

## 2. Create a group named users
```bash
groupadd users
```

## 3. Create the following user accounts
### harry's account
```shell
-harry
    - Primary group `admins`
    - Secondary group `users`
    - User ID 3455
```
`Note`: When we create a user in Linux, for example harry, then by Linux default settings harry's group name will also be harry.
```bash
#On the exam use 'help' to searh
useradd -h
# or as below
useradd -h | less
//primary
```
Now, let us create harry's account:
```bash
useradd -g admins -G users -u 3455 harry
```
### natasha
-natasha
    - Supplementary groups: `admins` and `users`
```bash
useradd -G admins,users -u 3456 natasha
```
## sarah
-sarah
    - Must not be a member of the `admins` group
    - Must not hav access to an interactive shell
`NOTE`: A nologin shell is considered a non-interactive shell
```bash
which nologin
/usr/sbin/nologin      #fullpath
#use useradd -h to find out about '-s' flag

useradd -s /sbin/nologin sarah
```

## bruce
   -bruce
    - Member of `admins` group
    - Home direcctory must be created explicitly
```bash
#use useradd -h to find out about '-m' flag
useradd -m -G admins bruce
```
## Set the password for all created users to: `password`
We have two ways to create passwords:
```bash
# Option 1
password harry
password natasha
password sarah
password bruce

# Option 2
echo "password" | passwd --stdin harry
echo "password" | passwd --stdin natasha
echo "password" | passwd --stdin sarah
echo "password" | passwd --stdin bruce

# Option 3 - Adhoc Command meaning "A one line command - one liner"!
for user in harry natasha sarah bruce: do echo "password" | passwd --stdin "$user; done
```

## Validate account data

```bash
id harry
id natasha
id sarah
id bruce
getent group admin
getent passwd harry natasha sarah bruce
pwck -r
grpck -r
```

### Step 5: Test Sarah’s restriction

```bash
su - sarah
#or
runuser -l sarah -c 'id'
```

## Rollback or Cleanup
```bash
userdel -r harry
userdel -r natasha
userdel -r sarah
groupdel admin
```