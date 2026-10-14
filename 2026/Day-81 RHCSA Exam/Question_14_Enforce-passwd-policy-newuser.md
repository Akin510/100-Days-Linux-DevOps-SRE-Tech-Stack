# Exam Question #14: Enforce Password Policies for New Users


## 1. Exam Question #14
On **Node1**, configure the system so that `all newly created users` mmeet the following password requirements::
   * Passwords must expire after 30 days
   * Passwords must be at least `9 characters long`

## Exam Question #5 Solution
### Solution Question 1:
> This Question Tests your usage of /etc/logins.def!
* This is where we configure `password aging parameters`
```bash
vi /etc/login.defs
# Password aging controls:
#
#       PASS_MAX_DAYS   Maximum number of days a password may be used.
#       PASS_MIN_DAYS   Minimum number of days allowed between password changes.
#       PASS_MIN_LEN    Minimum acceptable password length.
#       PASS_WARN_AGE   Number of days warning given before a password expires.
#
PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_WARN_AGE   7

#Make the change as per RHCSA Exam Question
PASS_MAX_DAYS   30
PASS_MIN_DAYS   0
PASS_WARN_AGE   7
:wq!
```
### Solution Question 2:
> This Question Tests your usage of /etc/security/pwquality.conf
* This is where we configure `password Length (LEN)`
```bash
# Using Find Command in the exam if you need help
find /etc -type f -name "*pwquality*"
/etc/security/pwquality.conf
#Let us open this file and make the changes:
vi /etc/security/pwquality.conf
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default). (See pam_cracklib manual.)
# Cannot be set to lower value than 6.
# minlen = 8

NOTE minlen = 8 is Commented "#". Remove the hash
minlen = 9
:wq!
```

### Validate - Use "chage -l" command
```bash
useradd testuser
chage -l testuser
[root@RedhatExam security]# chage -l nadeem
Last password change                                    : Oct 14, 2026
Password expires                                        : Nov 13, 2026
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 30
Number of days of warning before password expires       : 7

# Test password length "less than < 9" and "greater than > 9"
passwd testuser
Changing password for user testuser
New password:
BAD PASSWORD: The password is shorter than 9 characters
Retype new password:
passwd: 
```
# YOU HAVE SUCCESSFULLY COMPLETED QUESTION 14 OF THE RHCSA EXAM