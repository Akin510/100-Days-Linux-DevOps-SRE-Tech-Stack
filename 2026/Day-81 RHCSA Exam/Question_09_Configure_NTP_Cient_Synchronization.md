# Exam Question #09: Configure Network Time Protocol (NTP) Client Synchronization 


## 1. Exam Question #09
On **Node1**, configure the system to synchronize its system time with the NTP server time.google.com and meet the gollowing requirements
   * Configure Node1 to use `time.google.com` as its only time source.
   * Ensure time synchronization is enable and active.
   * The configuration must persist across reboots.
   * Verify that the system clock is synchronized with the configured NTP server

## Exam Question #09 Solution
### Solution Question 1:
Run:
```bash
#NTP SERVERS
cat /etc/chrony.conf
[root@RedhatExam ~]# chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? ntp.nyc.icanbwell.com         2   6     3     0  +3519us[+3519us] +/-   39ms
^? 2001:470:4143::10             1   6     3     1    +14ms[  +14ms] +/-   31ms
^? ntp5-2.mattnordhoffdns.c>     2   6     3     1    -11ms[  -11ms] +/-   67ms
^? s2-a.time.mci1.us.rozint>     2   6     3     1    +17ms[  +17ms] +/-   37ms

#Let us add time.google.com NTP server:
vi /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (https://www.pool.ntp.org/join.html).
#pool 2.rocky.pool.ntp.org iburst
server time.google.com iburst
:wq!
# Validate
[root@RedhatExam ~]# chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? ntp.nyc.icanbwell.com         2   6     3     0  +3519us[+3519us] +/-   39ms
^? 2001:470:4143::10             1   6     3     1    +14ms[  +14ms] +/-   31ms
^? ntp5-2.mattnordhoffdns.c>     2   6     3     1    -11ms[  -11ms] +/-   67ms
^? s2-a.time.mci1.us.rozint>     2   6     3     1    +17ms[  +17ms] +/-   37ms

#NOTE: If you don't restart chronyd the changes will not show
[root@RedhatExam ~]# systemctl restart chronyd
[root@RedhatExam ~]# chronyc sources -v

  .-- Source mode  '^' = server, '=' = peer, '#' = local clock.
 / .- Source state '*' = current best, '+' = combined, '-' = not combined,
| /             'x' = may be in error, '~' = too variable, '?' = unusable.
||                                                 .- xxxx [ yyyy ] +/- zzzz
||      Reachability register (octal) -.           |  xxxx = adjusted offset,
||      Log2(Polling interval) --.      |          |  yyyy = measured offset,
||                                \     |          |  zzzz = estimated error.
||                                 |    |           \
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? time4.google.com              1   6     3     1   -294us[ -294us] +/- 7797us

```

## Solution Questin 2/3: time is enabled, active and persists and persists across reboots:
> Look at `Loaded` line carefully. If you miss this you will miss it for life:
```bash
systemctl is-active chronyd
systemctl is-enabled chronyd
[root@RedhatExam ~]# systemctl status chronyd
● chronyd.service - NTP client/server
     Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-10-20 00:06:09 CDT; 3min 38s ago
       Docs: man:chronyd(8)
             man:chrony.conf(5)
   Main PID: 47720 (chronyd)
```

## Solution Question 4:
```bash
[root@RedhatExam ~]# timedatectl
[root@RedhatExam ~]# timedatectl -h
timedatectl [OPTIONS...] COMMAND ...

Query or change system time and date settings.

Commands:
  status                   Show current time settings
  show                     Show properties of systemd-timedated
  set-time TIME            Set system time
  set-timezone ZONE        Set system time zone
  list-timezones           Show known time zones
  set-local-rtc BOOL       Control whether RTC is in local time
  set-ntp BOOL             Enable or disable network time synchronization

[root@RedhatExam ~]# timedatectl
               Local time: Tue 2026-10-20 00:21:01 CDT
           Universal time: Tue 2026-10-20 05:21:01 UTC
                 RTC time: Tue 2026-10-20 05:21:01
                Time zone: America/Chicago (CDT, -0500)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

timedatectl set-ntp true
```


