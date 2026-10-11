# Wireshark Practical Lab: Build a Web Server and Analyze HTTP Traffic

## Lab Title

**Build an Apache Web Server on Rocky Linux and Analyze the Traffic with Wireshark**

---

## Lab Purpose

In this lab, students will:

1. Install the Apache HTTP web server on Rocky Linux.
2. Create a simple `index.html` web page.
3. Start and enable the Apache service.
4. Verify that Apache is listening on TCP port 80.
5. Configure the firewall to allow HTTP traffic.
6. Access the web page from a Windows client.
7. Capture the traffic in Wireshark.
8. Identify DNS, IP, TCP, and HTTP information.
9. Analyze TCP flags and TCP conversations.
10. Apply Wireshark display filters.

---

# Lab Environment

You need two systems.

## System 1 — Rocky Linux Web Server

Example:

```text
Hostname: webserver
Operating System: Rocky Linux 9
IP Address: 192.168.1.50  <------- (Use your actual IP addresss for your Virtual Machine)
Web Service: Apache/httpd
TCP Port: 80
```

Your actual IP address may be different.

---

## System 2 — Windows Client

The Windows client should have:

- Wireshark installed
- PowerShell
- Network connectivity to the Rocky Linux server (Via OpenVPN)

Example:
> NOTE: The IP's below will be different for your machines!!!
```text
Windows IP: 192.168.1.100
Rocky Linux IP: 192.168.1.50
```

---

# Part 1 — Check the Rocky Linux Server IP Address

Log in to the Rocky Linux server.

Run:

```bash
ip a
```

You can also use:

```bash
ip -br addr
```

Example output:

```text
enX0    UP    192.168.1.50/24
```

Record your server IP address.

```text
My Rocky Linux Server IP: _______________________
```

---

# Part 2 — Test Network Connectivity

From the Windows computer, open PowerShell.

Ping the Rocky Linux server:

```powershell
ping 192.168.1.50
```

Replace `192.168.1.50` with your actual Rocky Linux server IP.

Example:

```text
Reply from 192.168.1.50: bytes=32 time<1ms TTL=64
```

If ping fails, troubleshoot network connectivity before continuing.

---

# Part 3 — Install Apache HTTP Server

On the Rocky Linux server, install Apache:

```bash
dnf install httpd -y
```

Verify that the package was installed:

```bash
rpm -q httpd
```

Example:

```text
httpd-2.4.x-x.el9.x86_64
```

---

# Part 4 — Start and Enable Apache

Run:

```bash
systemctl enable --now httpd
```

This command performs two actions:

```text
enable = start Apache automatically when Linux boots
--now  = start Apache immediately
```

Check the service:

```bash
systemctl status httpd
```

You should see:

```text
Active: active (running)
```

You can press:

```text
q
```

to exit the status screen.

---

# Part 5 — Verify That TCP Port 80 Is Listening

Run:

```bash
ss -lntp
```

Look for port 80.

You can filter the output:

```bash
ss -lntp | grep :80
```

Example:

```text
LISTEN 0 511 0.0.0.0:80 0.0.0.0:*
```

This tells us that Apache is listening for TCP connections on port 80.

---

# Part 6 — Create the Web Page

The default Apache website directory on Rocky Linux is:

```text
/var/www/html
```

Make sure the directory exists:

```bash
mkdir -p /var/www/html
```

Move into the directory:

```bash
cd /var/www/html
```

Check your current directory:

```bash
pwd
```

You should see:

```text
/var/www/html
```

---

# Part 7 — Create index.html

Create the file using a text editor:

```bash
vi index.html
```

Press:

```text
i
```

to enter Insert mode.

Enter the following HTML:

```html
<!DOCTYPE html>
<html>
<head>
    <title>NIT Academy Wireshark Lab</title>
</head>

<body>

    <h1>Our Wireshark Test Website</h1>

    <h2>Welcome to the Networking Practical Lab</h2>

    <p>This website is running on a Rocky Linux Apache web server.</p>

    <p>We will capture this HTTP traffic using Wireshark.</p>

    <hr>

    <h3>Lab Information</h3>

    <ul>
        <li>Protocol: HTTP</li>
        <li>Transport Protocol: TCP</li>
        <li>Server Port: 80</li>
        <li>Web Server: Apache HTTP Server</li>
        <li>Operating System: Rocky Linux</li>
    </ul>

</body>
</html>
```

Press:

```text
Esc
```

Then save and exit:

```text
:wq
```

---

# Part 8 — Verify index.html

Run:

```bash
cat /var/www/html/index.html
```

You should see the HTML that you entered.

You can also check the file:

```bash
ls -l /var/www/html/index.html
```

---

# Part 9 — Test the Website Locally

Before involving Windows or Wireshark, test Apache directly from the Rocky Linux server.

Run:

```bash
curl http://localhost
```

You should see your HTML page returned.

You can also run:

```bash
curl http://127.0.0.1
```

If you see the HTML content, Apache is serving the page correctly.

---

# Part 10 — Check the Rocky Linux Firewall

Check whether firewalld is running:

```bash
systemctl status firewalld
```

Check the active firewall configuration:

```bash
firewall-cmd --list-all
```

Check allowed services:

```bash
firewall-cmd --list-services
```

If `http` is not listed, add it.

```bash
firewall-cmd --permanent --add-service=http
```

Reload the firewall:

```bash
firewall-cmd --reload
```

Verify again:

```bash
firewall-cmd --list-services
```

You should now see:

```text
http
```

among the allowed services.

---

# Important Firewall Concept

There are two common ways to allow HTTP.

## Method 1 — Allow the predefined HTTP service

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
```

## Method 2 — Allow TCP port 80 directly

```bash
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --reload
```

For this lab, use the service method:

```bash
firewall-cmd --permanent --add-service=http
```

You do not need to configure both methods.

---

# Part 11 — Verify Firewall Settings

Run:

```bash
firewall-cmd --list-all
```

Look for:

```text
services: ... http ...
```

You can also check specifically:

```bash
firewall-cmd --query-service=http
```

Expected output:

```text
yes
```

---

# Part 12 — Test the Website from Windows

On the Windows computer, open a web browser.

Enter:

```text
http://192.168.1.50
```

Replace the IP address with your Rocky Linux server IP.

You should see:

```text
Our Wireshark Test Website

Welcome to the Networking Practical Lab
```

---

# Part 13 — Test Using Windows PowerShell

You can also test with PowerShell.

Run:

```powershell
curl http://192.168.1.50
```

or:

```powershell
Invoke-WebRequest http://192.168.1.50
```

The response should contain your web page.

---

# Part 14 — Prepare Wireshark

Open Wireshark on the Windows computer.

You will see network interfaces such as:

```text
Ethernet
Wi-Fi
VPN
Loopback
```

Choose the interface that your Windows computer is using to communicate with the Rocky Linux server.

For example:

```text
Ethernet
```

or:

```text
Wi-Fi
```

Do not start the capture yet.

---

# Part 15 — Close Other Network Applications

For a cleaner capture:

- Close unnecessary browser tabs.
- Close applications that generate network traffic.
- Stop streaming applications.
- Avoid opening other websites during the lab.

This reduces background traffic.

---

# Part 16 — Start a Wireshark Capture

Click the correct interface.

Then click:

```text
Start Capturing Packets
```

or double-click the interface.

Wireshark will begin capturing packets in real time.

---

# Part 17 — Generate HTTP Traffic

After Wireshark starts capturing, open PowerShell.

Run:

```powershell
curl http://192.168.1.50
```

You can run it once or twice.

You can also refresh the web page in your browser.

---

# Part 18 — Stop the Capture

Return to Wireshark.

Click the red:

```text
Stop Capturing Packets
```

button.

You now have a packet capture containing the HTTP conversation.

---

# Part 19 — Understand the Wireshark Screen

Wireshark has three important panes.

## Pane 1 — Packet List

The top pane shows packets.

Important columns include:

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

The middle pane shows protocol details.

You may see:

```text
Frame
Ethernet II
Internet Protocol Version 4
Transmission Control Protocol
Hypertext Transfer Protocol
```

---

## Pane 3 — Packet Bytes

The bottom pane displays the raw packet data in:

```text
Hexadecimal
ASCII
```

---

# Part 20 — Filter HTTP Traffic

In the Wireshark display filter box, enter:

```text
http
```

Press Enter.

You should now see HTTP packets such as:

```text
GET /
HTTP/1.1 200 OK
```

---

# Part 21 — Find the HTTP GET Request

Look for a packet showing something similar to:

```text
GET / HTTP/1.1
```

Select it.

Expand:

```text
Hypertext Transfer Protocol
```

You should be able to identify:

```text
Request Method: GET
Request URI: /
Request Version: HTTP/1.1
```

---

# Part 22 — Find the HTTP Response

Look for:

```text
HTTP/1.1 200 OK
```

This means the Apache server successfully returned the web page.

Select the packet.

Expand:

```text
Hypertext Transfer Protocol
```

You may see:

```text
Response Code: 200
Response Phrase: OK
```

---

# Part 23 — View the TCP Three-Way Handshake

Remove the HTTP filter by clicking the `X` beside the display filter.

Now enter:

```text
tcp.port == 80
```

Look for the TCP handshake.

You should see something similar to:

```text
Client                              Server

SYN        ------------------------>

           <------------------- SYN, ACK

ACK        ------------------------>
```

---

# Part 24 — Understand the TCP Handshake

## Packet 1 — SYN

The client sends:

```text
SYN
```

Meaning:

```text
"I would like to establish a TCP connection."
```

---

## Packet 2 — SYN, ACK

The server responds:

```text
SYN, ACK
```

Meaning:

```text
"I received your request and I agree to establish the connection."
```

---

## Packet 3 — ACK

The client responds:

```text
ACK
```

Meaning:

```text
"I received your response. The TCP connection is established."
```

---

# Part 25 — Examine Source and Destination IP Addresses

Select any packet going from Windows to Rocky Linux.

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

---

# Part 26 — Examine TCP Ports

Expand:

```text
Transmission Control Protocol
```

You may see:

```text
Source Port: 52344
Destination Port: 80
```

The client source port is normally an ephemeral port.

The server destination port is:

```text
80
```

TCP port 80 normally represents:

```text
HTTP
```

---

# Part 27 — Examine TCP Flags

Expand:

```text
Transmission Control Protocol
```

Then expand:

```text
Flags
```

You may see:

```text
SYN
ACK
FIN
RST
PSH
```

The most important flags for this lab are:

| Flag | Meaning |
|---|---|
| SYN | Start a TCP connection |
| ACK | Acknowledge received data |
| FIN | Gracefully close a connection |
| RST | Immediately reset a connection |
| PSH | Deliver data to the application promptly |

---

# Part 28 — Find a Specific Frame Number

To display only packet number 5, enter:

```text
frame.number == 5
```

Wireshark will display only frame 5.

Students can now investigate:

- Source IP
- Destination IP
- Source port
- Destination port
- TCP flags
- Protocol information

---

# Part 29 — Identify the TCP Stream

Select a TCP packet.

Expand:

```text
Transmission Control Protocol
```

Look for:

```text
Stream index
```

Example:

```text
Stream index: 0
```

Wireshark assigns a stream number to each TCP conversation.

---

# Part 30 — Filter a TCP Conversation

If the stream number is `0`, enter:

```text
tcp.stream eq 0
```

If your stream number is `1`, enter:

```text
tcp.stream eq 1
```

Only packets belonging to that TCP conversation will now be displayed.

---

# Part 31 — Easier Way to Filter the Conversation

Instead of typing the filter manually:

1. Right-click one of the TCP packets.
2. Select:

```text
Conversation Filter
```

3. Select:

```text
TCP
```

Wireshark automatically creates the appropriate filter.

For example:

```text
tcp.stream eq 0
```

---

# Part 32 — Count Packets in the TCP Conversation

After applying:

```text
tcp.stream eq 0
```

look at the bottom of Wireshark.

You may see:

```text
Packets: 50
Displayed: 12
```

This means:

```text
50 total packets were captured.

12 packets belong to the currently displayed TCP conversation.
```

---

# Part 33 — Difference Between Captured and Displayed Packets

This is an important Wireshark concept.

## Packets

```text
Packets
```

means the total number of packets captured.

## Displayed

```text
Displayed
```

means the number of packets that match the current display filter.

Example:

```text
Packets: 125
Displayed: 9
```

means:

```text
125 packets exist in the capture.

9 packets match the current filter.
```

---

# Part 34 — DNS Traffic

Now generate DNS traffic from the Windows machine.

In PowerShell:

```powershell
nslookup example.com
```

or:

```powershell
Resolve-DnsName example.com
```

Capture the traffic in Wireshark.

Then apply:

```text
dns
```

You should see DNS query and response packets.

---

# Part 35 — Identify DNS Query and Response

A typical DNS exchange looks like:

```text
Windows Client                      DNS Server

DNS Query        ---------------------->

                 <---------------- DNS Response
```

Select a DNS query packet.

Expand:

```text
Domain Name System
```

Look for:

```text
Queries
```

You may see:

```text
example.com
Type: A
```

An `A` record asks for an IPv4 address.

---

# Part 36 — Wireshark Practical Questions

Answer the following questions from your capture.

---

## Question 1

**How many packets were captured?**

Answer:

```text
_____________________________________
```

Hint:

Look at the bottom of Wireshark or use:

```text
Statistics → Capture File Properties
```

---

## Question 2

**How many DNS packets are in the capture?**

Apply:

```text
dns
```

Answer:

```text
_____________________________________
```

---

## Question 3

**If Wireshark was installed for the first time, which profile are you normally using?**

Answer:

```text
_____________________________________
```

Hint:

Check:

```text
Edit → Configuration Profiles
```

---

## Question 4

**Look at packet number 5. What is the destination IP address?**

Filter:

```text
frame.number == 5
```

Expand:

```text
Internet Protocol Version 4
```

Answer:

```text
_____________________________________
```

---

## Question 5

**What is the destination TCP port in packet number 5?**

Expand:

```text
Transmission Control Protocol
```

Answer:

```text
_____________________________________
```

---

## Question 6

**What application normally uses this destination port?**

Answer:

```text
_____________________________________
```

---

## Question 7

**What TCP flag or flags are set in packet number 5?**

Expand:

```text
Transmission Control Protocol → Flags
```

Answer:

```text
_____________________________________
```

---

## Question 8

**What is the frame number of the next packet in this TCP conversation?**

Find the TCP stream number.

Example:

```text
Stream index: 0
```

Then filter:

```text
tcp.stream eq 0 && frame.number > 5
```

Answer:

```text
_____________________________________
```

---

## Question 9

**Can you create a filter for this TCP conversation? How many packets belong to the conversation?**

Example:

```text
tcp.stream eq 0
```

TCP Stream Number:

```text
_____________________________________
```

Number of packets displayed:

```text
_____________________________________
```

---

# Part 37 — Additional Student Challenges

## Challenge 1

Find the client's IP address.

Answer:

```text
_____________________________________
```

---

## Challenge 2

Find the server's IP address.

Answer:

```text
_____________________________________
```

---

## Challenge 3

Find the client's TCP source port.

Answer:

```text
_____________________________________
```

---

## Challenge 4

Find the server's TCP destination port.

Answer:

```text
_____________________________________
```

---

## Challenge 5

Find the first TCP SYN packet.

Frame Number:

```text
_____________________________________
```

---

## Challenge 6

Find the SYN-ACK packet.

Frame Number:

```text
_____________________________________
```

---

## Challenge 7

Find the final ACK of the three-way handshake.

Frame Number:

```text
_____________________________________
```

---

## Challenge 8

Find the HTTP GET request.

Frame Number:

```text
_____________________________________
```

---

## Challenge 9

What HTTP method was used?

Answer:

```text
_____________________________________
```

---

## Challenge 10

What HTTP response status code did the server return?

Answer:

```text
_____________________________________
```

Expected successful response:

```text
200 OK
```

---

# Part 38 — Useful Wireshark Display Filters

## Show only DNS

```text
dns
```

## Show only HTTP

```text
http
```

## Show TCP port 80

```text
tcp.port == 80
```

## Show traffic involving the web server

```text
ip.addr == 192.168.1.50
```

## Show only traffic going to the server

```text
ip.dst == 192.168.1.50
```

## Show only traffic coming from the server

```text
ip.src == 192.168.1.50
```

## Show packet number 5

```text
frame.number == 5
```

## Show TCP SYN packets

```text
tcp.flags.syn == 1
```

## Show SYN packets that are not ACKs

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

## Show SYN-ACK packets

```text
tcp.flags.syn == 1 && tcp.flags.ack == 1
```

## Show TCP reset packets

```text
tcp.flags.reset == 1
```

## Show one TCP stream

```text
tcp.stream eq 0
```

---

# Part 39 — Follow a TCP Stream

Wireshark can reconstruct the application conversation.

Select an HTTP packet.

Right-click it.

Choose:

```text
Follow → TCP Stream
```

A new window appears showing the data exchanged between the client and server.

You may see the HTTP request:

```text
GET / HTTP/1.1
Host: 192.168.1.50
```

and the server response:

```text
HTTP/1.1 200 OK
```

You may also see the contents of your HTML page.

This demonstrates that traditional HTTP traffic is not encrypted.

---

# Part 40 — Why HTTP Can Be Read in Wireshark

HTTP on port 80 does not normally encrypt application data.

Therefore Wireshark may display information such as:

```text
GET /
Host
User-Agent
HTTP headers
Web page content
```

This is one reason HTTPS is normally preferred for real websites.

HTTPS normally uses:

```text
TCP port 443
```

and encrypts the application data using TLS.

---

# Part 41 — Relating the Capture to the OSI Model

The captured HTTP traffic can be mapped to the OSI model.

| OSI Layer | What Students See |
|---|---|
| Layer 7 — Application | HTTP |
| Layer 6 — Presentation | Data representation |
| Layer 5 — Session | Application session |
| Layer 4 — Transport | TCP and port 80 |
| Layer 3 — Network | IPv4 addresses |
| Layer 2 — Data Link | Ethernet and MAC addresses |
| Layer 1 — Physical | Cable, Wi-Fi, NIC signals |

In Wireshark, a packet may appear as:

```text
Frame
 └── Ethernet II
      └── Internet Protocol Version 4
           └── Transmission Control Protocol
                └── Hypertext Transfer Protocol
```

This is an excellent demonstration of protocol encapsulation.

---

# Part 42 — Observe MAC Addresses

Select a packet.

Expand:

```text
Ethernet II
```

Look for:

```text
Source
Destination
```

These represent MAC addresses.

Example:

```text
Source:      aa:bb:cc:dd:ee:ff
Destination: 11:22:33:44:55:66
```

MAC addresses operate at:

```text
OSI Layer 2 — Data Link Layer
```

---

# Part 43 — Observe IP Addresses

Expand:

```text
Internet Protocol Version 4
```

Look for:

```text
Source Address
Destination Address
```

IP addresses operate at:

```text
OSI Layer 3 — Network Layer
```

---

# Part 44 — Observe TCP Ports

Expand:

```text
Transmission Control Protocol
```

Look for:

```text
Source Port
Destination Port
```

TCP operates at:

```text
OSI Layer 4 — Transport Layer
```

---

# Part 45 — Observe HTTP

Expand:

```text
Hypertext Transfer Protocol
```

HTTP operates at:

```text
OSI Layer 7 — Application Layer
```

---

# Part 46 — Packet Flow Summary

The complete lab flow is:

```text
Windows Client
     |
     |
     | DNS Query
     v
DNS Server
     |
     | DNS Response
     v
Windows Client
     |
     |
     | TCP SYN
     v
Rocky Linux Apache Server
     |
     | SYN-ACK
     v
Windows Client
     |
     | ACK
     v
Rocky Linux Apache Server
     |
     |
     | HTTP GET /
     v
Apache
     |
     |
     | HTTP 200 OK
     | index.html
     v
Windows Client
```

---

# Part 47 — Linux Verification Commands

Students should know the following commands by the end of the lab.

## Check Apache

```bash
systemctl status httpd
```

## Start Apache

```bash
systemctl start httpd
```

## Stop Apache

```bash
systemctl stop httpd
```

## Restart Apache

```bash
systemctl restart httpd
```

## Enable Apache at boot

```bash
systemctl enable httpd
```

## Enable and immediately start Apache

```bash
systemctl enable --now httpd
```

## Check listening ports

```bash
ss -lntp
```

## Check port 80

```bash
ss -lntp | grep :80
```

## Check firewall

```bash
firewall-cmd --list-all
```

## Check firewall services

```bash
firewall-cmd --list-services
```

## Test HTTP locally

```bash
curl http://localhost
```

---

# Part 48 — Troubleshooting

## Problem: Apache Will Not Start

Check:

```bash
systemctl status httpd
```

Then:

```bash
journalctl -u httpd
```

---

## Problem: Port 80 Is Not Listening

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

## Problem: Windows Cannot Reach the Website

First ping the server:

```powershell
ping 192.168.1.50
```

Then test port 80:

```powershell
Test-NetConnection 192.168.1.50 -Port 80
```

Look for:

```text
TcpTestSucceeded : True
```

---

## Problem: Ping Works but HTTP Does Not

Check Apache:

```bash
systemctl status httpd
```

Check port 80:

```bash
ss -lntp | grep :80
```

Check firewall:

```bash
firewall-cmd --list-all
```

Confirm HTTP is allowed:

```bash
firewall-cmd --query-service=http
```

---

## Problem: Wireshark Shows No Traffic

Verify that you selected the correct network interface.

Try:

```text
Ethernet
```

or:

```text
Wi-Fi
```

Start the capture again and run:

```powershell
curl http://192.168.1.50
```

---

# Part 49 — Optional Controlled Capture

If you want a cleaner packet capture, you can use a Wireshark capture filter before starting the capture.

Example:

```text
host 192.168.1.50 and tcp port 80
```

This tells Wireshark to capture only TCP port 80 traffic involving the server.

Be careful:

A **capture filter** determines what Wireshark records.

A **display filter** determines what Wireshark shows after packets have already been captured.

---

# Capture Filter vs Display Filter

## Capture Filter

Used before or while capturing.

Example:

```text
host 192.168.1.50 and tcp port 80
```

Meaning:

```text
Capture only HTTP-related TCP traffic involving this server.
```

---

## Display Filter

Used after packets are captured.

Examples:

```text
http
```

```text
tcp.port == 80
```

```text
frame.number == 5
```

```text
tcp.stream eq 0
```

Display filters do not delete packets.

They only control which captured packets are visible.

---

# Part 50 — Final Lab Validation

Before completing the lab, verify all of the following.

- [ ] Apache is installed.
- [ ] Apache is running.
- [ ] Apache is enabled at boot.
- [ ] `/var/www/html/index.html` exists.
- [ ] The custom website loads.
- [ ] TCP port 80 is listening.
- [ ] HTTP is allowed through firewalld.
- [ ] Windows can ping the Linux server.
- [ ] Windows can connect to TCP port 80.
- [ ] Wireshark can capture the HTTP traffic.
- [ ] Students can identify the TCP three-way handshake.
- [ ] Students can identify source and destination IP addresses.
- [ ] Students can identify TCP ports.
- [ ] Students can identify TCP flags.
- [ ] Students can locate an HTTP GET request.
- [ ] Students can locate an HTTP 200 OK response.
- [ ] Students can filter a TCP stream.
- [ ] Students can count the packets in a TCP conversation.
- [ ] Students understand capture filters vs display filters.

---

# Lab Completion Questions

Students should be able to explain the following in their own words:

1. What is Apache?
2. What is the purpose of TCP port 80?
3. What command shows listening TCP ports in Linux?
4. Why must the firewall allow HTTP?
5. What is a TCP three-way handshake?
6. What does the SYN flag mean?
7. What does the ACK flag mean?
8. What is the difference between a source port and destination port?
9. What is an ephemeral port?
10. What is an HTTP GET request?
11. What does HTTP status code `200 OK` mean?
12. What is the difference between a capture filter and a display filter?
13. What does `tcp.stream eq 0` do?
14. How does Wireshark help troubleshoot network problems?
15. Why can ordinary HTTP traffic be read in Wireshark?

---

# End of Lab

You have now:

```text
Built a Linux web server
        ↓
Configured Apache
        ↓
Created an HTML web page
        ↓
Opened TCP port 80
        ↓
Connected from Windows
        ↓
Captured the traffic
        ↓
Analyzed Ethernet
        ↓
Analyzed IPv4
        ↓
Analyzed TCP
        ↓
Analyzed HTTP
        ↓
Filtered a TCP conversation
```

This lab connects Linux administration, networking, TCP/IP, the OSI model, firewall configuration, web services, and packet analysis in one practical exercise.
