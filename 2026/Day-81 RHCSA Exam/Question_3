# EXAM QUESTION #3: Configuring and Securing an Apache HTTP Service

> Remember `Apache HTTP Server` = this is the official name of the software package you install (dnf install httpd)..
On Node1, configure the `Apache HTTP Server` to meet the following requirements:
* The Apache web service must be installed, enabled, and running
* The web server must listen on TCP port 85
* The Service must be accessible from `both the local system and external hosts.`
* When curled or accessed via a web browser, the server must display the following message:
      `Welcome to the Apache Web Server!`


## Exam Question #3 Solution
---
### Solution Question 1
In Question #2 we have already configured a file pointing to the BaseOS and AppStream Respositories that we have in-house.
Run:
```bash
systemctl status httpd
Unit httpd.service could not be found.
```
#Run:
```Bash
dnf install httpd -y
---------------
Installed:
  httpd-2.4.62-13.el9_8.9.x86_64        httpd-core-2.4.62-13.el9_8.9.x86_64  httpd-filesystem-2.4.62-13.el9_8.9.noarch
  mailcap-2.1.49-5.el9.0.2.noarch       mod_http2-2.0.26-6.el9_8.2.x86_64    mod_lua-2.4.62-13.el9_8.9.x86_64
  rocky-logos-httpd-90.17-1.el9.noarch

Complete!
systemctl enable --now httpd
```

#Check Service Status:
```bash
systemctl status httpd
[root@RedhatExam ~]# systemctl status httpd
○ httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: inactive (dead)
       Docs: man:httpd.service(8)
```

#enable `httpd` service
```bash
systemctl enable --now httpd
systemctl status httpd
```
---

### Solution Questkon 2: Configure Apache to Listen on Port 85
Open the main configuration file:
```bash
#Where is Apache Listening now?
curl localhost:80

#Let us configure the new port. Let us change the port number from 80 to 85
find /etc -type f -name "*http*"
/etc/logrotate.d/httpd
/etc/httpd/conf/httpd.conf

#Open the file:
vi /etc/httpd/conf/httpd.conf
 Listen: Allows you to bind Apache to specific IP addresses and/or
# ports, instead of the default. See also the <VirtualHost>
# directive.
#
# Change this to Listen on a specific IP address, but note that if
# httpd.service is enabled to run at boot time, the address may not be
# available when the service starts.  See the httpd.service(8) man
# page for more information.
#
#Listen 12.34.56.78:80
Listen 80
#Find the line that says Listen 80 and change it to:
Listen 85
:wq!

#Let us apply the configuration changes to the system:
systemctl restart httpd
ss -tulpn | grep httpd
#Also test
curl localhost:85
```
### Configure SELinux for Port 85
```bash
#Exam Trick!
man semanage-port
EXAMPLE
       List all port definitions
       # semanage port -l
       Allow Apache to listen on tcp port 81 (i.e. assign tcp port 81 label http_port_t, which apache is allowed to listen on)
       # semanage port -a -t http_port_t -p tcp 81

#Run
semanage port -a -t http_port_t -p tcp 85
semanage port -l | grep http
systemctl restart httpd
curl localhost:85
```

# You have successfully completed RHCSA Exam Question 8