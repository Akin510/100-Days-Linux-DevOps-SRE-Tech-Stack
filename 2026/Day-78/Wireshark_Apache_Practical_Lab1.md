# Wireshark Practical Lab
## Building an Apache Web Server and Analyzing DNS, TCP, and HTTP Traffic

---

# Lab Overview

In this practical lab, Nexus Team will build a simple web server using **Apache HTTP Server (`httpd`) on Rocky Linux** and then use **Wireshark on a Windows workstation** to capture and analyze the network traffic generated when accessing the website.

This lab combines several important IT concepts:

- Linux administration
- Apache web server administration
- HTML
- TCP/IP networking
- DNS
- TCP ports
- TCP flags
- TCP three-way handshake
- HTTP
- Firewalld
- Wireshark
- Packet analysis
- TCP conversations
- OSI model

---

# Learning Objectives

By the end of this lab, students should be able to:

1. Install Apache on Rocky Linux.
2. Start and enable a Linux service using `systemctl`.
3. Create a basic HTML web page.
4. Understand the Apache document root.
5. Verify that a service is listening on a TCP port.
6. Configure `firewalld` to allow HTTP traffic.
7. Test connectivity between Windows and Linux.
8. Capture packets using Wireshark.
9. Identify DNS packets.
10. Identify source and destination IP addresses.
11. Identify TCP source and destination ports.
12. Identify TCP flags.
13. Recognize the TCP three-way handshake.
14. Identify HTTP requests and responses.
15. Filter a specific TCP conversation.
16. Understand the difference between capture filters and display filters.
17. Relate Ethernet, IP, TCP, and HTTP to the OSI model.

---

# Lab Architecture

We will use two machines.

```text
+---------------------------+
|      Windows Client       |
|                           |
| Wireshark                 |
| PowerShell                |
| Web Browser               |
|                           |
| Example IP:               |
| 192.168.1.100             |
+-------------+-------------+
              |
              |
              | Ethernet / Wi-Fi
              |
              v
+-------------+-------------+
|     Rocky Linux Server    |
|                           |
| Apache HTTP Server        |
| httpd                     |
|                           |
| TCP Port: 80              |
|                           |
| Example IP:               |
| 192.168.1.50              |
+---------------------------+
```

Your IP addresses may be different.

---

# Lab Requirements

## Rocky Linux Server

The Linux machine should have:

- Rocky Linux 9
- Root or sudo access
- Network connectivity
- An IP address reachable from Windows

Example:

```text
Hostname: webserver
Operating System: Rocky Linux 9
IP Address: 192.168.1.50  <------- (Use your actual IP addresss for your Virtual Machine)
Web Service: Apache/httpd
TCP Port: 80
```

---

## Windows Client

The Windows machine should have:

- Wireshark
- PowerShell
- Web browser
- Network connectivity to the Rocky Linux server

Example:
> NOTE: The IP's below will be different for your machines!!!
```text
Windows IP: 192.168.1.100
Rocky Linux IP: 192.168.1.50
```

---

# Part 1 — Log In to the Rocky Linux Server

Log in to your Rocky Linux server.

Verify your current user:

```bash
whoami
```

If you are not logged in as root, use `sudo` before administrative commands.

---

# Part 2 — Find the Rocky Linux IP Address

Run:

```bash
ip addr
```

A shorter version is:

```bash
ip -br addr
```

Example:

```text
lo               UNKNOWN        127.0.0.1/8
enX0             UP             192.168.1.50/24
```

In this example, the server IP address is:

```text
192.168.1.50
```

Record your server IP.

```text
Rocky Linux Server IP: ______________________________
```

---

# Part 3 — Test Connectivity from Windows

On the Windows computer, open PowerShell.

Ping the Rocky Linux server.

Example:

```powershell
ping 192.168.1.50
```

Replace `192.168.1.50` with your server's actual address.

Successful output may look similar to:

```text
Reply from 192.168.1.50: bytes=32 time<1ms TTL=64
Reply from 192.168.1.50: bytes=32 time<1ms TTL=64
Reply from 192.168.1.50: bytes=32 time<1ms TTL=64
Reply from 192.168.1.50: bytes=32 time<1ms TTL=64
```

If ping works, basic IP connectivity exists.

---

# Part 4 — Install Apache HTTP Server

Return to the Rocky Linux server.

Install Apache:

```bash
dnf install -y httpd
```

Apache is called:

```text
httpd
```

on Rocky Linux, Red Hat Enterprise Linux, AlmaLinux, and similar distributions.

---

# Part 5 — Verify Apache Was Installed

Run:

```bash
rpm -q httpd
```

Example:

```text
httpd-2.4.62-1.el9.x86_64
```

The exact version may be different.

---

# Part 6 — Start Apache

Start the Apache service:

```bash
systemctl start httpd
```

Check its status:

```bash
systemctl status httpd
```

Look for:

```text
Active: active (running)
```

Press:

```text
q
```

to leave the status screen.

---

# Part 7 — Enable Apache at Boot

Run:

```bash
systemctl enable httpd
```

This configures Apache to start automatically when Linux starts.

You can verify:

```bash
systemctl is-enabled httpd
```

Expected result:

```text
enabled
```

---

# Part 8 — Start and Enable a Service with One Command

Instead of running:

```bash
systemctl start httpd
systemctl enable httpd
```

Linux administrators commonly use:

```bash
systemctl enable --now httpd
```

The command means:

```text
enable  = start automatically during boot

--now   = start the service immediately
```

---

# Part 9 — Verify Apache Is Running

Run:

```bash
systemctl is-active httpd
```

Expected:

```text
active
```

---

# Part 10 — Understand the Apache Document Root

The default Apache website directory on Rocky Linux is:

```text
/var/www/html
```

This directory is commonly called the:

```text
Document Root
```

Apache serves files stored inside this directory.

---

# Part 11 — Create the Website Directory

Make sure the directory exists:

```bash
mkdir -p /var/www/html
```

Move into it:

```bash
cd /var/www/html
```

Verify your current directory:

```bash
pwd
```

Expected:

```text
/var/www/html
```

---

# Part 12 — Create index.html

Create the web page:

```bash
vi index.html
```

Press:

```text
i
```

to enter Insert Mode.

Enter the following HTML.

```html
<!DOCTYPE html>
<html>

<head>
    <title>NIT Academy Wireshark Lab</title>
</head>

<body>

    <h1>Our Wireshark Test Website</h1>

    <h2>Welcome to the Networking Practical Lab</h2>

    <p>
        This website is running on a Rocky Linux Apache web server.
    </p>

    <p>
        We are going to capture and analyze this HTTP traffic
        using Wireshark.
    </p>

    <hr>

    <h3>Lab Information</h3>

    <ul>
        <li>Operating System: Rocky Linux</li>
        <li>Web Server: Apache HTTP Server</li>
        <li>Application Protocol: HTTP</li>
        <li>Transport Protocol: TCP</li>
        <li>Server Port: TCP 80</li>
        <li>Packet Analyzer: Wireshark</li>
    </ul>

    <hr>

    <h3>What We Are Learning</h3>

    <p>
        We will identify Ethernet frames, IP addresses,
        TCP ports, TCP flags, HTTP requests, and HTTP responses.
    </p>

</body>

</html>
```

---

# Part 13 — Save the HTML File

Press:

```text
Esc
```

Then type:

```text
:wq
```

and press Enter.

This means:

```text
w = write/save
q = quit
```

---

# Part 14 — Verify index.html Exists

Run:

```bash
ls -l /var/www/html
```

You should see:

```text
index.html
```

Display the contents:

```bash
cat /var/www/html/index.html
```

---

# Part 15 — Important Linux File Concept

Do **not** run:

```bash
mkdir -p /var/www/html/index.html
```

That would create a **directory called `index.html`**.

We want:

```text
/var/www/html/
```

to be a directory and:

```text
index.html
```

to be a file.

Correct structure:

```text
/var
 └── www
      └── html
           └── index.html
```

---

# Part 16 — Test Apache Locally

Before testing from Windows, test the website directly from the Rocky Linux server.

Run:

```bash
curl http://localhost
```

You should see your HTML.

You can also run:

```bash
curl http://127.0.0.1
```

If the HTML appears, Apache is successfully serving the page.

---

# Part 17 — Verify TCP Port 80 Is Listening

Run:

```bash
ss -lntp
```

To look specifically for port 80:

```bash
ss -lntp | grep :80
```

Example:

```text
LISTEN 0 511 0.0.0.0:80 0.0.0.0:*
```

You may also see:

```text
[::]:80
```

---

# What Does LISTEN Mean?

`LISTEN` means that a program is waiting for incoming TCP connections.

In our case:

```text
Apache
   |
   v
TCP Port 80
   |
   v
LISTEN
```

Clients can attempt to connect to that port.

---

# Part 18 — Understand the ss Command

The command:

```bash
ss -lntp
```

uses several options.

| Option | Meaning |
|---|---|
| `-l` | Show listening sockets |
| `-n` | Do not resolve service names |
| `-t` | Show TCP sockets |
| `-p` | Show the associated process |

---

# Part 19 — Check firewalld

Check whether the firewall service is running:

```bash
systemctl status firewalld
```

You should normally see:

```text
Active: active (running)
```

---

# Part 20 — View the Current Firewall Configuration

Run:

```bash
firewall-cmd --list-all
```

Example:

```text
public (active)
  target: default
  interfaces: enX0
  services: cockpit dhcpv6-client ssh
  ports:
```

Notice that `http` may not yet be allowed.

---

# Part 21 — Allow HTTP Through the Firewall

Run:

```bash
firewall-cmd --permanent --add-service=http
```

Expected:

```text
success
```

Reload firewalld:

```bash
firewall-cmd --reload
```

Expected:

```text
success
```

---

# Part 22 — Verify HTTP Is Allowed

Run:

```bash
firewall-cmd --list-services
```

You should now see something similar to:

```text
cockpit dhcpv6-client http ssh
```

You can also run:

```bash
firewall-cmd --query-service=http
```

Expected:

```text
yes
```

---

# Part 23 — Firewall Service vs Firewall Port

We allowed HTTP using:

```bash
firewall-cmd --permanent --add-service=http
```

Firewalld already knows that the `http` service uses:

```text
TCP port 80
```

Another method would have been:

```bash
firewall-cmd --permanent --add-port=80/tcp
```

But there is no need to configure both.

For this lab use:

```bash
--add-service=http
```

---

# Part 24 — Test the Website from Windows

Return to the Windows machine.

Open a browser.

Enter:

```text
http://192.168.1.50
```

Replace the IP with your Rocky Linux server address.

You should see:

# Our Wireshark Test Website

and the rest of your HTML content.

---

# Part 25 — Test TCP Port 80 from Windows

Open PowerShell.

Run:

```powershell
Test-NetConnection 192.168.1.50 -Port 80
```

Look for:

```text
TcpTestSucceeded : True
```

This proves that the Windows client can reach TCP port 80 on the Linux server.

---

# Part 26 — Test HTTP from PowerShell

Use:

```powershell
curl.exe http://192.168.1.50/
```

We specifically use:

```text
curl.exe
```

so Windows uses the actual curl executable.

You should see the HTML returned by Apache.

---

# Part 27 — Prepare Wireshark

Open Wireshark on the Windows computer.

You may see interfaces such as:

```text
Ethernet
Wi-Fi
VPN
Loopback
```

Choose the network interface that communicates with your Rocky Linux server.

For example:

```text
Ethernet
```

or:

```text
Wi-Fi
```

---

# Part 28 — Wireshark Profile

If Wireshark has just been installed and no custom profiles have been created, the profile normally being used is:

```text
Default
```

You can inspect profiles using:

```text
Edit
   ↓
Configuration Profiles
```

---

# Part 29 — Our Original Wireshark Questions

During this lab we want to answer:

1. How many packets were captured in this file?
2. How many DNS packets are in the file?
3. If you just installed Wireshark for the first time, what profile are you using?
4. Look at packet number 5 — what is the destination IP address?
5. What is the destination TCP port in this same packet?
6. What application typically uses this port?
7. What TCP flag is set in this packet?
8. What is the frame number of the next packet in this TCP conversation?
9. Can you set a filter for this conversation? How many packets do you get?

---

# Part 30 — Why We Will Create a Controlled Capture

A normal computer generates many background packets.

For example:

```text
ARP
DNS
HTTPS
Windows Update
OneDrive
Browser traffic
Cloud applications
Broadcast traffic
IPv6 traffic
```

If all of this traffic is captured, packet number 5 could be almost anything.

Therefore we will create a **controlled capture**.

---

# Part 31 — Our Controlled Packet Sequence

We want approximately this sequence:

```text
Packet 1    DNS Query
Packet 2    DNS Response

Packet 3    TCP SYN
Packet 4    TCP SYN, ACK
Packet 5    TCP ACK

Packet 6    HTTP GET
Packet 7+   HTTP response / TCP packets
```

This makes packet number 5 particularly useful for analysis.

---

# Part 32 — Configure a Wireshark Capture Filter

Before starting the capture, enter a capture filter.

Assume the Rocky Linux server is:

```text
192.168.1.50
```

Use:

```text
(udp port 53 and host 8.8.8.8) or (tcp port 80 and host 192.168.1.50)
```

Replace:

```text
192.168.1.50
```

with your actual server IP.

---

# What Does This Filter Capture?

The first section:

```text
udp port 53 and host 8.8.8.8
```

captures DNS traffic involving Google DNS.

The second section:

```text
tcp port 80 and host 192.168.1.50
```

captures HTTP-related TCP traffic involving our Apache server.

---

# Part 33 — Start the Capture

Click:

```text
Start Capturing Packets
```

Do not browse the Internet while performing this section.

---

# Part 34 — Generate One DNS Query

Immediately open PowerShell.

Run:

```powershell
Resolve-DnsName example.com -Type A -Server 8.8.8.8 -DnsOnly
```

This generates a DNS request for:

```text
example.com
```

We specifically request:

```text
Type A
```

which asks for an IPv4 address.

---

# Part 35 — Generate the HTTP Connection

Now run:

```powershell
curl.exe http://192.168.1.50/
```

Replace the IP address with your web server IP.

You should see your HTML page returned.

---

# Part 36 — Stop the Capture

Immediately return to Wireshark.

Click:

```text
Stop Capturing Packets
```

We now have a controlled capture.

---

# Part 37 — Expected Packet Pattern

Your exact numbers can differ slightly, but the beginning should resemble:

```text
No.     Protocol      Information
---     --------      -----------

1       DNS           Standard query A example.com

2       DNS           Standard query response A example.com

3       TCP           Client → 80 [SYN]

4       TCP           80 → Client [SYN, ACK]

5       TCP           Client → 80 [ACK]

6       HTTP          GET /

7       TCP/HTTP      Response

8       HTTP          HTTP/1.1 200 OK

...
```

---

# Important Note

Packet numbering can occasionally differ because of:

- Retransmissions
- Network behavior
- Operating system behavior
- DNS behavior
- Existing connections

The instructor should verify the capture before asking students to analyze packet number 5.

The important objective is learning **how to find the answers**, not memorizing specific packet numbers.

---

# Part 38 — Understand the Wireshark Window

Wireshark contains three main panes.

---

## Pane 1 — Packet List

The upper pane shows captured packets.

Typical columns:

```text
No.
Time
Source
Destination
Protocol
Length
Info
```

---

## Pane 2 — Packet Details

The middle pane breaks the packet into protocols.

For HTTP traffic, you may see:

```text
Frame
Ethernet II
Internet Protocol Version 4
Transmission Control Protocol
Hypertext Transfer Protocol
```

---

## Pane 3 — Packet Bytes

The bottom pane displays the raw bytes.

The data appears as:

```text
Hexadecimal
ASCII
```

---

# Question 1 — How Many Packets Were Captured?

Look at the bottom status bar.

You may see:

```text
Packets: 12
Displayed: 12
```

Another method is:

```text
Statistics
   ↓
Capture File Properties
```

Look for:

```text
Packets
```

Record the answer.

```text
ANSWER:

Total Packets Captured: ______________________
```

---

# Important Concept — Packets vs Displayed

Suppose Wireshark says:

```text
Packets: 100
Displayed: 15
```

This means:

```text
100 packets were captured.

15 packets currently match the display filter.
```

---

# Question 2 — How Many DNS Packets Are in the Capture?

In the green Wireshark display filter box enter:

```text
dns
```

Press Enter.

Look at:

```text
Displayed:
```

at the bottom of Wireshark.

For our controlled lab we normally expect approximately:

```text
2
```

because there should be:

```text
DNS Query
DNS Response
```

Record the actual result.

```text
ANSWER:

Number of DNS Packets: ______________________
```

---

# Examine the DNS Query

Select the DNS query.

Expand:

```text
Domain Name System
```

Then expand:

```text
Queries
```

You should find:

```text
example.com
```

and:

```text
Type: A
```

---

# What Does DNS Do?

DNS stands for:

```text
Domain Name System
```

It converts names such as:

```text
example.com
```

into IP addresses.

Conceptually:

```text
example.com
       |
       | DNS
       v
93.x.x.x
```

---

# Question 3 — Which Wireshark Profile Is Being Used?

On a new Wireshark installation, the normal profile is:

```text
Default
```

Check:

```text
Edit
   ↓
Configuration Profiles
```

Record your answer.

```text
ANSWER:

Wireshark Profile: ___________________________
```

---

# Question 4 — Examine Packet Number 5

Remove the DNS filter.

Enter:

```text
frame.number == 5
```

Press Enter.

Only frame 5 should appear.

Select the packet.

---

# Examine the IP Header

Expand:

```text
Internet Protocol Version 4
```

Look for:

```text
Source Address
Destination Address
```

Example:

```text
Source Address:      192.168.1.100
Destination Address: 192.168.1.50
```

For our controlled lab, the destination should normally be the Apache server.

Record it.

```text
ANSWER:

Destination IP Address: ______________________
```

---

# Question 5 — What Is the Destination TCP Port?

While still examining frame 5, expand:

```text
Transmission Control Protocol
```

Look for:

```text
Source Port
Destination Port
```

Example:

```text
Source Port:      53124
Destination Port: 80
```

Record the destination port.

```text
ANSWER:

Destination TCP Port: ________________________
```

---

# Client Port vs Server Port

A connection might look like:

```text
Windows Client                       Apache Server

192.168.1.100                        192.168.1.50

TCP 53124  ----------------------->  TCP 80
   ^                                     ^
   |                                     |
Ephemeral                           HTTP Server
Client Port                            Port
```

The client's source port is usually dynamically selected.

The server listens on a known service port.

---

# Question 6 — Which Application Typically Uses This Port?

TCP port:

```text
80
```

is normally used by:

```text
HTTP
```

HTTP stands for:

```text
Hypertext Transfer Protocol
```

Record the answer.

```text
ANSWER:

Application Protocol: ________________________
```

---

# Common Well-Known Ports

| Port | Protocol |
|---:|---|
| 20/21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 143 | IMAP |
| 443 | HTTPS |

---

# Question 7 — What TCP Flag Is Set?

Continue examining frame 5.

Expand:

```text
Transmission Control Protocol
```

Then expand:

```text
Flags
```

For the third packet in a normal TCP handshake you should see:

```text
ACK
```

Record the actual result.

```text
ANSWER:

TCP Flag(s): _________________________________
```

---

# Part 39 — TCP Three-Way Handshake

Before HTTP data can normally be transferred using TCP, a connection must be established.

The process is called the:

```text
TCP Three-Way Handshake
```

It looks like:

```text
WINDOWS CLIENT                         APACHE SERVER

      |                                     |
      |------------- SYN ------------------>|
      |                                     |
      |<---------- SYN, ACK ----------------|
      |                                     |
      |------------- ACK ------------------>|
      |                                     |
      |        TCP CONNECTION READY         |
      |                                     |
```

---

# Step 1 — SYN

The client sends:

```text
SYN
```

Conceptually:

```text
"I want to establish a TCP connection."
```

---

# Step 2 — SYN-ACK

The server responds:

```text
SYN, ACK
```

Conceptually:

```text
"I received your request and I am ready."
```

---

# Step 3 — ACK

The client sends:

```text
ACK
```

Conceptually:

```text
"I received your response."
```

The connection is now established.

---

# Part 40 — Filter SYN Packets

Use:

```text
tcp.flags.syn == 1
```

This will show packets where the SYN bit is set.

---

# Show the Initial Client SYN Only

Use:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

---

# Show SYN-ACK Packets

Use:

```text
tcp.flags.syn == 1 && tcp.flags.ack == 1
```

---

# Part 41 — Find the TCP Stream Number

Return to frame 5:

```text
frame.number == 5
```

Expand:

```text
Transmission Control Protocol
```

Find:

```text
Stream index
```

Example:

```text
Stream index: 0
```

Record your stream number.

```text
TCP Stream Number: ___________________________
```

---

# What Is a TCP Stream?

A TCP stream represents one TCP conversation between two endpoints.

For example:

```text
192.168.1.100:53124
          |
          |
          | TCP Conversation
          |
          v
192.168.1.50:80
```

Wireshark may assign this conversation:

```text
tcp.stream 0
```

Another TCP connection could become:

```text
tcp.stream 1
```

and so on.

---

# Question 8 — Find the Next Packet in the TCP Conversation

Suppose frame 5 belongs to:

```text
tcp.stream 0
```

Use:

```text
tcp.stream eq 0 && frame.number > 5
```

The first displayed packet is the next packet in the same TCP conversation.

For our controlled lab this may be:

```text
Frame 6
```

which could contain the HTTP GET request.

Record the actual result.

```text
ANSWER:

Next Frame Number: ___________________________
```

---

# Question 9 — Filter the Entire TCP Conversation

If the stream is:

```text
0
```

use:

```text
tcp.stream eq 0
```

If it is stream 1:

```text
tcp.stream eq 1
```

Look at the bottom of Wireshark.

Example:

```text
Packets: 15
Displayed: 9
```

That means:

```text
9 packets belong to this TCP stream.
```

Record the result.

```text
ANSWER:

TCP Stream Number: ___________________________

Packets in TCP Conversation: _________________
```

---

# Part 42 — Graphical Method for Filtering a TCP Conversation

You do not have to type the stream filter manually.

Select a packet from the TCP connection.

Right-click it.

Choose:

```text
Conversation Filter
      ↓
TCP
```

Wireshark automatically creates something similar to:

```text
tcp.stream eq 0
```

---

# Part 43 — Generate and Examine HTTP Traffic

Apply:

```text
http
```

Look for:

```text
GET /
```

Select the packet.

Expand:

```text
Hypertext Transfer Protocol
```

You may see:

```text
Request Method: GET
Request URI: /
Request Version: HTTP/1.1
```

---

# What Is HTTP GET?

The client is requesting a resource from the server.

For example:

```text
GET /
```

means:

```text
"Send me the default page from the root of the website."
```

Apache then returns:

```text
index.html
```

---

# Part 44 — Find the HTTP Response

Look for something similar to:

```text
HTTP/1.1 200 OK
```

Select the packet.

Expand:

```text
Hypertext Transfer Protocol
```

You should find a response status.

Example:

```text
Status Code: 200
Response Phrase: OK
```

---

# What Does 200 OK Mean?

HTTP status:

```text
200 OK
```

means the request was successfully processed.

In our example:

```text
Client
   |
   | GET /
   v
Apache Server
   |
   | HTTP/1.1 200 OK
   | index.html
   v
Client
```

---

# Part 45 — Follow the TCP Stream

Select one of the HTTP packets.

Right-click.

Choose:

```text
Follow
   ↓
TCP Stream
```

Wireshark reconstructs the data exchanged between the client and server.

You may see something similar to:

```text
GET / HTTP/1.1
Host: 192.168.1.50
User-Agent: curl/...
Accept: */*
```

The server response may contain:

```text
HTTP/1.1 200 OK
```

and even the HTML we created:

```html
<h1>Our Wireshark Test Website</h1>
```

---

# Part 46 — Important Security Lesson

We are using:

```text
HTTP
TCP Port 80
```

HTTP traffic is normally **not encrypted**.

Therefore Wireshark may be able to read:

```text
HTTP request
HTTP headers
Requested URL
Web page content
Form data
Application data
```

This is one reason modern websites normally use:

```text
HTTPS
```

HTTPS normally uses:

```text
TCP Port 443
```

and protects the application data using TLS encryption.

---

# Part 47 — Examine the Ethernet Header

Select one of the HTTP/TCP packets.

Expand:

```text
Ethernet II
```

Look for:

```text
Source
Destination
```

These are MAC addresses.

Example:

```text
Source:      aa:bb:cc:dd:ee:ff
Destination: 00:11:22:33:44:55
```

MAC addresses belong primarily to:

```text
OSI Layer 2
Data Link Layer
```

---

# Part 48 — Examine the IP Header

Expand:

```text
Internet Protocol Version 4
```

Find:

```text
Source Address
Destination Address
```

IP operates at:

```text
OSI Layer 3
Network Layer
```

---

# Part 49 — Examine the TCP Header

Expand:

```text
Transmission Control Protocol
```

Find:

```text
Source Port
Destination Port
Sequence Number
Acknowledgment Number
Flags
Window Size
```

TCP operates at:

```text
OSI Layer 4
Transport Layer
```

---

# Part 50 — Examine HTTP

Expand:

```text
Hypertext Transfer Protocol
```

HTTP operates at:

```text
OSI Layer 7
Application Layer
```

---

# Protocol Encapsulation

Wireshark may display the packet approximately as:

```text
Frame
 |
 +-- Ethernet II
      |
      +-- Internet Protocol Version 4
           |
           +-- Transmission Control Protocol
                |
                +-- Hypertext Transfer Protocol
```

This demonstrates protocol encapsulation.

---

# Part 51 — Relate the Lab to the OSI Model

| OSI Layer | Protocol/Technology Seen in Lab |
|---|---|
| Layer 7 — Application | HTTP, DNS |
| Layer 6 — Presentation | Data representation |
| Layer 5 — Session | Application session |
| Layer 4 — Transport | TCP, UDP, ports |
| Layer 3 — Network | IPv4 |
| Layer 2 — Data Link | Ethernet, MAC addresses |
| Layer 1 — Physical | NIC, cable, Wi-Fi signals |

---

# Part 52 — Useful Wireshark Display Filters

## Display DNS

```text
dns
```

---

## Display HTTP

```text
http
```

---

## Display TCP Port 80

```text
tcp.port == 80
```

---

## Display Traffic Involving the Web Server

```text
ip.addr == 192.168.1.50
```

---

## Display Packets Going to the Server

```text
ip.dst == 192.168.1.50
```

---

## Display Packets Coming from the Server

```text
ip.src == 192.168.1.50
```

---

## Display Packet Number 5

```text
frame.number == 5
```

---

## Display TCP SYN Packets

```text
tcp.flags.syn == 1
```

---

## Display Initial SYN Packets

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

---

## Display SYN-ACK Packets

```text
tcp.flags.syn == 1 && tcp.flags.ack == 1
```

---

## Display TCP ACK Packets

```text
tcp.flags.ack == 1
```

---

## Display TCP FIN Packets

```text
tcp.flags.fin == 1
```

---

## Display TCP RST Packets

```text
tcp.flags.reset == 1
```

---

## Display One TCP Stream

```text
tcp.stream eq 0
```

---

## Display Packets After Frame 5 in Stream 0

```text
tcp.stream eq 0 && frame.number > 5
```

---

# Part 53 — Capture Filter vs Display Filter

This is one of the most important concepts in Wireshark.

---

## Capture Filter

A **capture filter** controls which packets Wireshark actually records.

For example:

```text
host 192.168.1.50 and tcp port 80
```

means:

```text
Only capture TCP port 80 traffic involving 192.168.1.50.
```

Packets that do not match the capture filter are not saved in the capture.

---

## Display Filter

A **display filter** controls which already-captured packets Wireshark displays.

Example:

```text
http
```

or:

```text
tcp.port == 80
```

or:

```text
frame.number == 5
```

The other captured packets still exist.

They are simply hidden from the current view.

---

# Example

Suppose Wireshark captured:

```text
Packets: 100
```

Then you apply:

```text
dns
```

and Wireshark says:

```text
Displayed: 4
```

This means:

```text
100 total packets exist.

4 of those packets are DNS packets.
```

---

# Part 54 — Student Practical Questions

Answer all questions using Wireshark.

---

## Question 1

How many packets were captured?

```text
Answer: ______________________________________
```

---

## Question 2

How many DNS packets are in the capture?

Filter:

```text
dns
```

```text
Answer: ______________________________________
```

---

## Question 3

If you just installed Wireshark for the first time, what profile are you using?

```text
Answer: ______________________________________
```

---

## Question 4

Look at packet number 5.

Filter:

```text
frame.number == 5
```

What is the destination IP address?

```text
Answer: ______________________________________
```

---

## Question 5

What is the destination TCP port in packet number 5?

```text
Answer: ______________________________________
```

---

## Question 6

What application typically uses this port?

```text
Answer: ______________________________________
```

---

## Question 7

What TCP flag or flags are set in packet number 5?

```text
Answer: ______________________________________
```

---

## Question 8

What is the frame number of the next packet in this TCP conversation?

```text
TCP Stream Number: ____________________________

Next Frame Number: ____________________________
```

---

## Question 9

Create a filter for the entire TCP conversation.

```text
Filter Used:

______________________________________________
```

How many packets belong to the conversation?

```text
Answer: ______________________________________
```

---

# Part 55 — Additional Student Challenges

## Challenge 1

What is the Windows client's IP address?

```text
Answer: ______________________________________
```

---

## Challenge 2

What is the Rocky Linux server's IP address?

```text
Answer: ______________________________________
```

---

## Challenge 3

What is the MAC address of the Windows client?

```text
Answer: ______________________________________
```

---

## Challenge 4

What is the MAC address of the destination device shown in the Ethernet frame?

```text
Answer: ______________________________________
```

---

## Challenge 5

What ephemeral TCP source port did the client use?

```text
Answer: ______________________________________
```

---

## Challenge 6

What TCP port is Apache listening on?

```text
Answer: ______________________________________
```

---

## Challenge 7

Find the first TCP SYN.

```text
Frame Number: _________________________________
```

---

## Challenge 8

Find the TCP SYN-ACK.

```text
Frame Number: _________________________________
```

---

## Challenge 9

Find the final ACK completing the handshake.

```text
Frame Number: _________________________________
```

---

## Challenge 10

Find the HTTP GET request.

```text
Frame Number: _________________________________
```

---

## Challenge 11

What HTTP request method was used?

```text
Answer: ______________________________________
```

---

## Challenge 12

What resource did the browser/client request?

```text
Answer: ______________________________________
```

---

## Challenge 13

What HTTP status code did Apache return?

```text
Answer: ______________________________________
```

---

## Challenge 14

What does HTTP status code `200` mean?

```text
Answer:

________________________________________________

________________________________________________
```

---

## Challenge 15

What is the TCP stream number?

```text
Answer: ______________________________________
```

---

# Part 56 — Linux Commands Students Should Know

## Display IP Addresses

```bash
ip addr
```

or:

```bash
ip -br addr
```

---

## Install Apache

```bash
dnf install -y httpd
```

---

## Start Apache

```bash
systemctl start httpd
```

---

## Stop Apache

```bash
systemctl stop httpd
```

---

## Restart Apache

```bash
systemctl restart httpd
```

---

## Check Apache Status

```bash
systemctl status httpd
```

---

## Enable Apache at Boot

```bash
systemctl enable httpd
```

---

## Enable and Start Apache

```bash
systemctl enable --now httpd
```

---

## Determine Whether Apache Is Active

```bash
systemctl is-active httpd
```

---

## Determine Whether Apache Is Enabled

```bash
systemctl is-enabled httpd
```

---

## Check Listening TCP Ports

```bash
ss -lntp
```

---

## Check TCP Port 80

```bash
ss -lntp | grep :80
```

---

## Check Firewall

```bash
firewall-cmd --list-all
```

---

## Check Firewall Services

```bash
firewall-cmd --list-services
```

---

## Allow HTTP

```bash
firewall-cmd --permanent --add-service=http
```

---

## Reload Firewall

```bash
firewall-cmd --reload
```

---

## Verify HTTP Firewall Rule

```bash
firewall-cmd --query-service=http
```

---

## Test Web Server Locally

```bash
curl http://localhost
```

---

# Part 57 — Windows Commands Students Should Know

## Ping Linux Server

```powershell
ping 192.168.1.50
```

---

## Test TCP Port 80

```powershell
Test-NetConnection 192.168.1.50 -Port 80
```

---

## Request the Website

```powershell
curl.exe http://192.168.1.50/
```

---

## Perform DNS Lookup

```powershell
Resolve-DnsName example.com
```

---

## Perform Controlled DNS Query

```powershell
Resolve-DnsName example.com -Type A -Server 8.8.8.8 -DnsOnly
```

---

# Part 58 — Troubleshooting

## Problem 1 — Apache Does Not Start

Check:

```bash
systemctl status httpd
```

Then examine logs:

```bash
journalctl -u httpd
```

---

# Problem 2 — Port 80 Is Not Listening

Run:

```bash
ss -lntp | grep :80
```

Restart Apache:

```bash
systemctl restart httpd
```

Check again:

```bash
ss -lntp | grep :80
```

---

# Problem 3 — Website Does Not Load

Test locally:

```bash
curl http://localhost
```

If that works, Apache itself is probably functioning.

Next check the firewall:

```bash
firewall-cmd --query-service=http
```

Expected:

```text
yes
```

---

# Problem 4 — Windows Cannot Reach TCP Port 80

From Windows:

```powershell
Test-NetConnection 192.168.1.50 -Port 80
```

If you see:

```text
TcpTestSucceeded : False
```

check:

1. Server IP address
2. Apache service
3. Port 80 listening
4. Firewalld
5. Network connectivity
6. VM network configuration

---

# Problem 5 — Windows Cannot Ping Linux

Check the Windows IP:

```powershell
ipconfig
```

Check the Linux IP:

```bash
ip -br addr
```

Verify that both systems have network connectivity.

---

# Problem 6 — Wireshark Captures Nothing

Verify that you selected the correct interface.

For example:

```text
Ethernet
```

or:

```text
Wi-Fi
```

Then generate traffic again:

```powershell
curl.exe http://192.168.1.50/
```

---

# Problem 7 — Packet Number 5 Is Not TCP ACK

This can happen because network captures are dynamic.

Do not assume every computer will generate exactly the same frame numbers.

Instead locate the handshake manually.

Filter:

```text
tcp.port == 80
```

Then identify:

```text
SYN
SYN, ACK
ACK
```

The third handshake packet is the ACK.

---

# Part 59 — Instructor Demonstration

The instructor can draw the following on the board.

```text
               DNS LOOKUP

Windows Client                 DNS Server
     |                             |
     |---------- Query ----------->|
     |                             |
     |<-------- Response ----------|
     |                             |


          TCP THREE-WAY HANDSHAKE

Windows Client                 Apache Server
     |                             |
     |----------- SYN ------------>|
     |                             |
     |<-------- SYN, ACK ----------|
     |                             |
     |----------- ACK ------------>|
     |                             |


                HTTP

Windows Client                 Apache Server
     |                             |
     |---------- GET / ----------->|
     |                             |
     |<------ 200 OK + HTML -------|
     |                             |
```

---

# Part 60 — Connect Ports to Applications

Explain:

```text
IP Address
    |
    | Identifies the computer
    v
192.168.1.50
    |
    |
    | TCP Port
    v
80
    |
    |
    | Application
    v
Apache / HTTP
```

An IP address identifies the host.

A TCP or UDP port helps identify the application/service on that host.

---

# Part 61 — Client and Server Ports

When the Windows client connects:

```text
Windows Client                 Linux Server

192.168.1.100                  192.168.1.50

Port 53124  -----------------> Port 80

Temporary                      HTTP
Ephemeral Port                 Well-known Port
```

The exact client source port will usually be different each time.

---

# Part 62 — Complete Communication Flow

Our complete lab looks like:

```text
Student Windows PC
      |
      |
      | DNS Query
      v
DNS Server
      |
      | DNS Response
      v
Student Windows PC
      |
      |
      | TCP SYN
      v
Rocky Linux Server
      |
      | TCP SYN-ACK
      v
Windows
      |
      | TCP ACK
      v
Rocky Linux
      |
      |
      | HTTP GET /
      v
Apache
      |
      |
      | Reads
      v
/var/www/html/index.html
      |
      |
      | HTTP 200 OK
      | HTML content
      v
Windows
      |
      |
      v
Web Page Displayed
```

---

# Part 63 — Final Verification Checklist

Before completing the lab, verify:

- [ ] Rocky Linux has a valid IP address.
- [ ] Windows can reach Rocky Linux.
- [ ] Apache is installed.
- [ ] Apache is running.
- [ ] Apache is enabled at boot.
- [ ] `/var/www/html/index.html` exists.
- [ ] The custom HTML page was created.
- [ ] `curl http://localhost` returns the page.
- [ ] TCP port 80 is listening.
- [ ] Firewalld allows the HTTP service.
- [ ] Windows can reach TCP port 80.
- [ ] Windows can retrieve the website.
- [ ] Wireshark captured DNS traffic.
- [ ] Wireshark captured TCP traffic.
- [ ] Wireshark captured HTTP traffic.
- [ ] Students identified a DNS query.
- [ ] Students identified a DNS response.
- [ ] Students identified TCP SYN.
- [ ] Students identified TCP SYN-ACK.
- [ ] Students identified TCP ACK.
- [ ] Students identified the destination IP.
- [ ] Students identified TCP port 80.
- [ ] Students identified the HTTP GET request.
- [ ] Students identified HTTP `200 OK`.
- [ ] Students identified a TCP stream.
- [ ] Students filtered an individual TCP conversation.
- [ ] Students counted the packets in a TCP conversation.
- [ ] Students understand capture filters.
- [ ] Students understand display filters.

---

# Part 64 — Final Review Questions

Students should be able to explain the following without simply memorizing commands.

### 1. What is Apache?

```text
________________________________________________

________________________________________________
```

### 2. What does the `httpd` service provide?

```text
________________________________________________

________________________________________________
```

### 3. What is `/var/www/html`?

```text
________________________________________________

________________________________________________
```

### 4. What is `index.html`?

```text
________________________________________________

________________________________________________
```

### 5. What does `systemctl enable --now httpd` do?

```text
________________________________________________

________________________________________________
```

### 6. What does `ss -lntp` show?

```text
________________________________________________

________________________________________________
```

### 7. Why must TCP port 80 be allowed through the firewall?

```text
________________________________________________

________________________________________________
```

### 8. What is TCP?

```text
________________________________________________

________________________________________________
```

### 9. What is a TCP port?

```text
________________________________________________

________________________________________________
```

### 10. What is an ephemeral port?

```text
________________________________________________

________________________________________________
```

### 11. What is the TCP three-way handshake?

```text
________________________________________________

________________________________________________
```

### 12. What does SYN mean?

```text
________________________________________________
```

### 13. What does ACK mean?

```text
________________________________________________
```

### 14. What does SYN-ACK mean?

```text
________________________________________________
```

### 15. What is HTTP?

```text
________________________________________________

________________________________________________
```

### 16. What does HTTP GET mean?

```text
________________________________________________

________________________________________________
```

### 17. What does HTTP `200 OK` mean?

```text
________________________________________________

________________________________________________
```

### 18. What does DNS do?

```text
________________________________________________

________________________________________________
```

### 19. What is the difference between a capture filter and a display filter?

```text
________________________________________________

________________________________________________
```

### 20. What does the following filter do?

```text
tcp.stream eq 0
```

```text
________________________________________________

________________________________________________
```

---

# Part 65 — Instructor Answer Guide for the Nine Main Questions

The exact packet counts and frame numbers depend on the student's capture.

## Question 1

**How many packets were captured?**

Answer:

```text
Use the total "Packets" value shown by Wireshark.
```

---

## Question 2

**How many DNS packets are in the capture?**

Use:

```text
dns
```

For the controlled lab, approximately:

```text
2
```

is expected.

---

## Question 3

**What profile is normally used on a fresh installation?**

Answer:

```text
Default
```

---

## Question 4

**What is the destination IP address in packet 5?**

Expected in our controlled capture:

```text
The Rocky Linux Apache server IP
```

Example:

```text
192.168.1.50
```

---

## Question 5

**What is the destination TCP port?**

Expected:

```text
80
```

---

## Question 6

**Which application/protocol normally uses this port?**

Answer:

```text
HTTP
```

---

## Question 7

**What TCP flag is set?**

If frame 5 is the final packet of our TCP handshake:

```text
ACK
```

---

## Question 8

**What is the next frame in the conversation?**

Use:

```text
tcp.stream eq X && frame.number > 5
```

The first displayed packet is the answer.

In the ideal controlled capture it may be:

```text
Frame 6
```

---

## Question 9

**How do you filter this TCP conversation?**

Example:

```text
tcp.stream eq 0
```

Use the Wireshark:

```text
Displayed:
```

counter to determine how many packets belong to the stream.

---

# Lab Summary

During this practical lab we:

```text
Installed Apache
       ↓
Started httpd
       ↓
Enabled httpd at boot
       ↓
Created /var/www/html/index.html
       ↓
Built our own HTML webpage
       ↓
Verified TCP port 80
       ↓
Configured firewalld
       ↓
Connected from Windows
       ↓
Generated DNS traffic
       ↓
Generated TCP traffic
       ↓
Generated HTTP traffic
       ↓
Captured everything with Wireshark
       ↓
Identified DNS
       ↓
Identified IP addresses
       ↓
Identified TCP ports
       ↓
Analyzed SYN
       ↓
Analyzed SYN-ACK
       ↓
Analyzed ACK
       ↓
Observed the TCP three-way handshake
       ↓
Identified HTTP GET
       ↓
Identified HTTP 200 OK
       ↓
Filtered a TCP conversation
       ↓
Followed a TCP stream
       ↓
Connected the traffic to the OSI model
```

---

# End of Wireshark Practical Lab

**NIT Academy — Linux and Networking Practical Lab**