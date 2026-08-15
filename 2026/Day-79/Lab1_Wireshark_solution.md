# Wireshark Lab Notes & Solutions

## Getting Started with Wireshark — Lab 1

### Lab Objective

In this lab, students will use Wireshark to examine a packet capture and learn how to identify:

- DNS queries and responses
- Source and destination IP addresses
- Source and destination TCP ports
- Common application ports
- TCP flags
- TCP conversations
- The TCP three-way handshake
- HTTP requests and responses
- Wireshark display filters
- TCP stream filters
- How multiple TCP connections can occur simultaneously

The most important part of this lab is understanding how a client establishes a TCP connection before HTTP communication begins.

---

# Lab Questions

Answer the following questions using the supplied Wireshark packet capture.

1. How many packets were captured in this file?
2. How many DNS packets are in the file?
3. If you just installed Wireshark for the first time, what profile are you using?
4. Look at packet number 5. What is the destination IP address?
5. What is the destination TCP port in this same packet?
6. What application typically uses this port?
7. What TCP flag is set in this packet?
8. What is the frame number of the next packet in this TCP conversation?
9. Can you set a filter for this conversation? How many packets do you get?

---

# Lab Answers

| Question | Answer |
|---|---|
| **1. How many packets were captured?** | **268 packets** |
| **2. How many DNS packets are in the capture?** | Apply the `dns` display filter and check the **Displayed** packet count. |
| **3. What Wireshark profile is being used?** | **Default** |
| **4. What is the destination IP in packet 5?** | **104.16.143.237** |
| **5. What is the destination TCP port?** | **80** |
| **6. What application normally uses TCP port 80?** | **HTTP** |
| **7. What TCP flag is set in packet 5?** | **SYN** |
| **8. What is the next frame in this TCP conversation?** | **Frame 9** |
| **9. Can the conversation be filtered?** | Yes. Use **Conversation Filter → TCP** or `tcp.stream eq X`. Check the **Displayed** count for the answer. |

---

# Part 1 — Understanding the Capture

The capture contains:

```text
268 packets
```

You can find this information by opening:

```text
Statistics
→ Capture File Properties
```

At the bottom of the Capture File Properties window, Wireshark displays statistics about the capture.

For this capture:

```text
Packets: 268
```

Therefore:

> **Question 1 Answer: 268 packets**

---

# Part 2 — Understanding the Beginning of the Capture

The first packets in the trace are DNS packets.

The capture begins approximately like this:

```text
Packet 1   DNS   Query A     www.udemy.com
Packet 2   DNS   Query AAAA  www.udemy.com
Packet 3   DNS   Response A
Packet 4   DNS   Response AAAA
```

This is important because the client initially knows the hostname:

```text
www.udemy.com
```

but needs an IP address before it can communicate with the web server.

The process is:

```text
www.udemy.com
      |
      v
     DNS
      |
      v
Find the server IP address
      |
      v
104.16.143.237
```

After DNS resolution completes, the client can begin communicating with the server.

---

# Part 3 — Finding the Number of DNS Packets

To answer Question 2, enter the following Wireshark display filter:

```text
dns
```

Press:

```text
Enter
```

Wireshark will now display only DNS packets.

At the bottom of Wireshark, look for:

```text
Packets: 268
Displayed: XX
```

The value beside:

```text
Displayed:
```

is the number of DNS packets.

For example:

```text
Packets: 268
Displayed: 18
```

would mean that there are 18 DNS packets in the capture.

> Record the actual **Displayed** number from your packet capture.

---

# Part 4 — Wireshark Profile

Look at the lower-right corner of Wireshark.

The screenshot shows:

```text
Profile: Default
```

Therefore:

> **Question 3 Answer: Default**

A fresh Wireshark installation normally begins with the Default profile unless another profile has been created or selected.

---

# Part 5 — Examining Packet 5

Now click:

```text
Packet 5
```

The packet summary contains information similar to:

```text
192.168.4.100 → 104.16.143.237
TCP
59610 → 80 [SYN]
Seq=0
Win=64240
Len=0
MSS=1460
SACK_PERM
```

This packet is especially important because it begins a TCP connection.

---

# Part 6 — Source and Destination

Packet 5 shows:

```text
Source IP:
192.168.4.100
```

and:

```text
Destination IP:
104.16.143.237
```

Therefore:

> **Question 4 Answer: 104.16.143.237**

The client is:

```text
192.168.4.100
```

The remote web server is:

```text
104.16.143.237
```

---

# Part 7 — Understanding the TCP Ports

Packet 5 shows:

```text
59610 → 80
```

This means:

```text
Source Port:      59610
Destination Port: 80
```

Therefore:

> **Question 5 Answer: TCP port 80**

---

## What is Port 59610?

Port:

```text
59610
```

is a temporary client-side port.

It is often called an:

```text
Ephemeral Port
```

The operating system temporarily chooses this port for the connection.

---

## What is Port 80?

TCP port:

```text
80
```

is the traditional well-known port for:

```text
HTTP
```

Therefore:

> **Question 6 Answer: HTTP**

The connection can therefore be represented as:

```text
Client                                  Web Server

192.168.4.100:59610  ------------->  104.16.143.237:80
```

---

# Part 8 — TCP SYN Flag

Packet 5 contains:

```text
[SYN]
```

Therefore:

> **Question 7 Answer: SYN**

SYN means:

```text
Synchronize
```

It is normally used to begin a TCP connection.

The client is essentially saying:

> I would like to establish a TCP connection with you.

---

# Part 9 — The TCP Three-Way Handshake

Before HTTP data can be exchanged, TCP must establish a connection.

TCP normally does this using the:

```text
Three-Way Handshake
```

The three steps are:

```text
1. SYN
2. SYN, ACK
3. ACK
```

Conceptually:

```text
CLIENT                                   SERVER

          SYN
--------------------------------------->

          SYN, ACK
<---------------------------------------

          ACK
--------------------------------------->

       CONNECTION ESTABLISHED
```

---

# Part 10 — TCP Handshake in This Capture

For the TCP connection beginning with packet 5, the handshake is:

```text
Packet 5     SYN
Packet 9     SYN, ACK
Packet 10    ACK
```

The conversation is:

```text
Client                                  Server
192.168.4.100                           104.16.143.237

59610 -------- SYN ------------------> 80
             Packet 5


59610 <------ SYN, ACK --------------- 80
             Packet 9


59610 -------- ACK ------------------> 80
             Packet 10
```

After packet 10, the TCP connection has been established.

---

# Part 11 — Understanding Packet 5 in Detail

Packet 5 contains a line similar to:

```text
59610 → 80 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM
```

Each field tells us something about the TCP connection.

| Field | Meaning |
|---|---|
| `59610` | Client source port |
| `80` | Server destination port |
| `[SYN]` | Request to begin a TCP connection |
| `Seq=0` | Relative TCP sequence number |
| `Win=64240` | TCP receive window |
| `Len=0` | No application data in this TCP segment |
| `MSS=1460` | Maximum Segment Size |
| `SACK_PERM` | Selective Acknowledgment is permitted |

For beginning students, concentrate primarily on:

```text
59610 → 80
[SYN]
Seq=0
Len=0
```

---

# Part 12 — Why Does Packet 5 Have `Len=0`?

Packet 5 shows:

```text
Len=0
```

This means that packet 5 is not carrying HTTP application data.

The packet's purpose is primarily to establish the TCP connection.

For example, the client has not yet transmitted:

```http
GET /
```

The sequence is:

```text
TCP connection first
        |
        v
TCP handshake
        |
        v
Connection established
        |
        v
HTTP request
```

This demonstrates an important concept:

> TCP must establish the transport-layer connection before HTTP communication can begin.

---

# Part 13 — Why Isn't Packet 6 the Response to Packet 5?

This is one of the most important lessons in the capture.

Students may initially assume:

```text
Packet 5
Packet 6
Packet 7
```

must belong together simply because the packet numbers are consecutive.

That is incorrect.

Look carefully at the TCP ports.

Packet 5 contains:

```text
59610 → 80 [SYN]
```

Packet 6 contains:

```text
59626 → 80 [SYN]
```

Notice the difference:

```text
Packet 5 source port = 59610
Packet 6 source port = 59626
```

These are different TCP connections.

---

# Part 14 — Two TCP Connections Are Happening at the Same Time

The computer is opening two TCP connections almost simultaneously.

## TCP Connection A

The first connection uses client port:

```text
59610
```

Its handshake is:

```text
Packet 5     59610 → 80     SYN
Packet 9     80 → 59610     SYN, ACK
Packet 10    59610 → 80     ACK
```

Diagram:

```text
CLIENT                                   SERVER
192.168.4.100                            104.16.143.237

59610 -------- SYN -------------------> 80
              #5

59610 <------ SYN, ACK ---------------- 80
              #9

59610 -------- ACK -------------------> 80
              #10
```

---

## TCP Connection B

A second connection uses client port:

```text
59626
```

Its handshake is:

```text
Packet 6     59626 → 80     SYN
Packet 7     80 → 59626     SYN, ACK
Packet 8     59626 → 80     ACK
```

Diagram:

```text
CLIENT                                   SERVER
192.168.4.100                            104.16.143.237

59626 -------- SYN -------------------> 80
              #6

59626 <------ SYN, ACK ---------------- 80
              #7

59626 -------- ACK -------------------> 80
              #8
```

---

# Part 15 — Comparing the Two Connections

The two conversations are:

```text
TCP Connection A

192.168.4.100:59610
        ↕
104.16.143.237:80
```

and:

```text
TCP Connection B

192.168.4.100:59626
        ↕
104.16.143.237:80
```

Notice:

- Same client IP
- Same server IP
- Same destination TCP port
- Same protocol
- Different client source ports

Therefore they are separate TCP conversations.

---

# Part 16 — The TCP/IP 5-Tuple

A TCP conversation can be identified using what is commonly called the:

```text
5-Tuple
```

The five items are:

```text
1. Source IP address
2. Source port
3. Destination IP address
4. Destination port
5. Protocol
```

For packet 5:

```text
Source IP:        192.168.4.100
Source Port:      59610

Destination IP:   104.16.143.237
Destination Port: 80

Protocol:         TCP
```

Together:

```text
TCP
192.168.4.100:59610
        ↕
104.16.143.237:80
```

This uniquely identifies this TCP communication flow.

---

# Part 17 — Finding the Next Packet in Packet 5's Conversation

Question 8 asks:

> What is the frame number of the next packet in this TCP conversation?

Packet 5 contains:

```text
59610 → 80
```

Now look at the following packets.

Packet 6:

```text
59626 → 80
```

Wrong connection.

Packet 7:

```text
80 → 59626
```

Still the connection using port 59626.

Packet 8:

```text
59626 → 80
```

Still the connection using port 59626.

Then packet 9:

```text
80 → 59610
```

This matches the connection started in packet 5.

Therefore:

> **Question 8 Answer: Frame 9**

Packet 9 is the server's response to packet 5.

---

# Part 18 — Packet 9: SYN, ACK

Packet 9 contains something similar to:

```text
80 → 59610 [SYN, ACK]
Seq=0
Ack=1
```

Two TCP flags are active:

```text
SYN
ACK
```

The server is essentially saying:

> I received your request to establish a connection, and I agree to establish the connection.

The handshake so far is:

```text
Client                         Server

SYN
----------------------------->

             SYN, ACK
<-----------------------------
```

---

# Part 19 — Understanding `Seq=0` and `Ack=1`

Packet 5 displays:

```text
Seq=0
```

Packet 9 displays:

```text
Ack=1
```

Wireshark normally displays relative TCP sequence numbers to make packet analysis easier.

Conceptually:

```text
Client:

SYN
Seq=0
```

The server responds:

```text
SYN, ACK
Ack=1
```

The server is essentially saying:

> I received your SYN and expect your next sequence number.

A TCP SYN consumes one sequence number, which helps explain the transition from:

```text
Seq=0
```

to:

```text
Ack=1
```

---

# Part 20 — Packet 10: Final ACK

Packet 10 contains:

```text
59610 → 80 [ACK]
```

This is the third step of the handshake.

```text
CLIENT                            SERVER

       SYN
-------------------------------->

       SYN, ACK
<--------------------------------

       ACK
-------------------------------->
```

After packet 10:

```text
TCP CONNECTION ESTABLISHED
```

The client and server can now exchange application data.

---

# Part 21 — Packet 11: HTTP GET

Immediately after the handshake, packet 11 shows HTTP communication.

Packet 11 contains approximately:

```text
HTTP
GET / HTTP/1.1
```

This is extremely useful for understanding the relationship between TCP and HTTP.

The order was:

```text
Packet 5     TCP SYN

Packet 9     TCP SYN, ACK

Packet 10    TCP ACK

--------------------------------
TCP connection established
--------------------------------

Packet 11    HTTP GET
```

Therefore:

> HTTP did not establish the network connection. TCP established the connection first, and HTTP then used that TCP connection.

---

# Part 22 — TCP and HTTP in the OSI Model

This can also be related to the OSI model.

```text
Application Layer
       HTTP
        |
        v
Transport Layer
       TCP
        |
        v
Network Layer
       IPv4
        |
        v
Data Link Layer
      Ethernet
```

HTTP operates at the application layer.

TCP operates at the transport layer.

IP operates at the network layer.

Ethernet operates at the data-link layer.

---

# Part 23 — Packet 13: HTTP Response

The server later responds with packet 13.

The capture shows something similar to:

```text
HTTP/1.1 301 Moved Permanently
```

This is an HTTP response from the web server.

Therefore the high-level sequence is:

```text
DNS
 |
 v
Resolve www.udemy.com
 |
 v
Obtain Server IP Address
 |
 v
TCP SYN
 |
 v
TCP SYN/ACK
 |
 v
TCP ACK
 |
 v
TCP Connection Established
 |
 v
HTTP GET
 |
 v
HTTP Response
```

---

# Part 24 — Complete Packet Flow

From the beginning of the capture, we can summarize the activity approximately as:

```text
PACKETS 1–4
DNS Resolution

www.udemy.com
      |
      v
104.16.143.237
```

Then:

```text
PACKET 5
TCP SYN

192.168.4.100:59610
        |
        v
104.16.143.237:80
```

Then:

```text
PACKET 9
TCP SYN, ACK

192.168.4.100:59610
        ^
        |
104.16.143.237:80
```

Then:

```text
PACKET 10
TCP ACK
```

Now:

```text
TCP CONNECTION ESTABLISHED
```

Then:

```text
PACKET 11
HTTP GET /
```

Then:

```text
PACKET 13
HTTP/1.1 301 Moved Permanently
```

---

# Part 25 — Filtering a TCP Conversation

Question 9 asks:

> Can you set a filter for this conversation?

Yes.

There are several ways.

---

## Method 1 — Conversation Filter

Select:

```text
Packet 5
```

Then right-click the packet.

Select:

```text
Conversation Filter
→ TCP
```

Wireshark will automatically create a display filter that isolates this TCP conversation.

Now look at the bottom of Wireshark.

You should see something similar to:

```text
Packets: 268
Displayed: XX
```

The:

```text
Displayed
```

value is the number of packets belonging to this conversation.

---

# Part 26 — Filtering by TCP Stream

Another excellent method is to use the TCP stream number.

Select packet 5.

Expand:

```text
Transmission Control Protocol
```

Look for:

```text
Stream index
```

For example, Wireshark might show:

```text
Stream index: 0
```

You could then use:

```text
tcp.stream eq 0
```

However, do not automatically assume that packet 5 is stream 0.

Always verify the actual:

```text
Stream index
```

first.

If Wireshark shows:

```text
Stream index: 2
```

then use:

```text
tcp.stream eq 2
```

---

# Part 27 — Why `tcp.stream` Is Useful

A TCP stream filter automatically follows both directions of the conversation.

For example:

```text
192.168.4.100:59610
        ↕
104.16.143.237:80
```

will include traffic going:

```text
Client → Server
```

and:

```text
Server → Client
```

This is much easier than manually filtering every IP address and TCP port.

---

# Part 28 — Alternative Manual Filter

Students can also manually build a TCP filter.

For example:

```text
tcp.port == 59610
```

This should show TCP packets involving port 59610.

A more specific filter could be:

```text
ip.addr == 192.168.4.100 &&
ip.addr == 104.16.143.237 &&
tcp.port == 59610
```

However, for learning conversations, the preferred method is:

```text
tcp.stream eq X
```

---

# Part 29 — Important Lesson: Packet Numbers Do Not Define a Conversation

One of the biggest mistakes new Wireshark students make is assuming that consecutive packet numbers belong to the same communication.

For example:

```text
Packet 5
Packet 6
Packet 7
Packet 8
Packet 9
```

does NOT mean all five packets belong to one TCP conversation.

Instead, examine:

```text
Source IP
Destination IP
Source Port
Destination Port
Protocol
```

For example:

```text
Packet 5:
59610 → 80

Packet 6:
59626 → 80
```

These belong to separate TCP connections.

Therefore:

> Always examine the TCP/IP conversation rather than simply following packet numbers.

---

# Part 30 — Important TCP Flags

Students should become familiar with the most common TCP flags.

| TCP Flag | General Meaning |
|---|---|
| `SYN` | Begin a TCP connection |
| `ACK` | Acknowledge received TCP information |
| `SYN, ACK` | Accept a TCP connection request |
| `FIN` | Gracefully close a TCP connection |
| `RST` | Immediately reset a TCP connection |
| `PSH` | Deliver data to the application promptly |

For this lab, the most important flags are:

```text
SYN
SYN, ACK
ACK
```

because they form the TCP three-way handshake.

---

# Part 31 — Simple Way to Remember the TCP Handshake

Think of it like a conversation.

### Step 1 — SYN

Client:

> Can we talk?

```text
Client -------- SYN --------> Server
```

### Step 2 — SYN/ACK

Server:

> Yes. I received your request. Can you hear me?

```text
Client <----- SYN/ACK ------- Server
```

### Step 3 — ACK

Client:

> Yes, I hear you.

```text
Client -------- ACK --------> Server
```

Now:

```text
CONNECTION ESTABLISHED
```

Application data can begin.

---

# Part 32 — The Complete Web Connection

A simplified browser connection can be understood as:

```text
User enters:

www.udemy.com
```

Then:

```text
Step 1
DNS Lookup

"What IP address belongs to www.udemy.com?"
```

DNS responds with an IP address.

Then:

```text
Step 2
TCP Three-Way Handshake

SYN
SYN/ACK
ACK
```

Then:

```text
Step 3
HTTP Request

GET /
```

Then:

```text
Step 4
HTTP Response

HTTP/1.1 301 Moved Permanently
```

Therefore:

```text
DNS
 ↓
TCP
 ↓
HTTP
```

---

# Part 33 — Packet Flow From This Lab

Students should be able to explain the following after completing the lab:

```text
DNS

Packet 1
Packet 2
Packet 3
Packet 4

       ↓

TCP CONNECTION A

Packet 5  = SYN
Packet 9  = SYN/ACK
Packet 10 = ACK

       ↓

HTTP

Packet 11 = HTTP GET
Packet 13 = HTTP Response
```

At the same time, another TCP connection was being established:

```text
TCP CONNECTION B

Packet 6 = SYN
Packet 7 = SYN/ACK
Packet 8 = ACK
```

---

# Part 34 — Visual Summary

```text
                       DNS
                        |
                        v
                www.udemy.com
                        |
                        v
                104.16.143.237
                        |
                        v

              TCP CONNECTION #1

192.168.4.100                         104.16.143.237
PORT 59610                                 PORT 80

       SYN
       #5
------------------------------------------>

                       SYN + ACK
                          #9
<------------------------------------------

       ACK
       #10
------------------------------------------>

               CONNECTION ESTABLISHED

                       |
                       v

                     HTTP

       HTTP GET /
       #11
------------------------------------------>

                   HTTP RESPONSE
                301 Moved Permanently
                       #13
<------------------------------------------
```

---

# Part 35 — Second Simultaneous TCP Connection

While the first connection is being established, another connection is also occurring.

```text
192.168.4.100                         104.16.143.237
PORT 59626                                 PORT 80

       SYN
       #6
------------------------------------------>

                       SYN + ACK
                          #7
<------------------------------------------

       ACK
       #8
------------------------------------------>

               CONNECTION ESTABLISHED
```

This demonstrates that one computer can have many simultaneous TCP connections to the same server.

The source port helps distinguish them.

---

# Part 36 — Key Concepts Students Should Remember

After completing this lab, students should understand the following concepts.

### DNS translates names into IP addresses

```text
www.udemy.com
        ↓
       DNS
        ↓
104.16.143.237
```

### TCP port 80 normally represents HTTP

```text
Destination TCP Port 80
        ↓
       HTTP
```

### A TCP connection starts with a three-way handshake

```text
SYN
SYN/ACK
ACK
```

### Packet 5 begins a TCP connection

```text
59610 → 80 [SYN]
```

### Packet 9 is packet 5's response

```text
80 → 59610 [SYN, ACK]
```

### Packet 10 completes the handshake

```text
59610 → 80 [ACK]
```

### HTTP comes after TCP

```text
TCP Handshake
     ↓
HTTP GET
     ↓
HTTP Response
```

### Multiple TCP conversations can occur simultaneously

```text
59610 ↔ 80

59626 ↔ 80
```

These are two different TCP connections.

---

# Part 37 — Useful Wireshark Filters

Students should practice the following display filters.

## Show DNS

```text
dns
```

---

## Show TCP

```text
tcp
```

---

## Show HTTP

```text
http
```

---

## Show Traffic to or from an IP

```text
ip.addr == 104.16.143.237
```

---

## Show Traffic Involving TCP Port 80

```text
tcp.port == 80
```

---

## Show Only TCP SYN Packets

```text
tcp.flags.syn == 1
```

---

## Show Initial SYN Packets but Exclude SYN/ACK

```text
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

This is especially useful for finding the beginning of TCP connections.

---

## Show SYN/ACK Packets

```text
tcp.flags.syn == 1 && tcp.flags.ack == 1
```

---

## Show a Specific Client Port

```text
tcp.port == 59610
```

---

## Show a Specific TCP Stream

```text
tcp.stream eq X
```

Replace `X` with the actual TCP stream index.

---

# Part 38 — Instructor Demonstration

A useful classroom demonstration is to ask students:

> What packet comes immediately after packet 5?

They will answer:

```text
Packet 6
```

Then ask:

> Is packet 6 actually the response to packet 5?

Compare the ports:

```text
Packet 5:

59610 → 80
```

and:

```text
Packet 6:

59626 → 80
```

They are different.

Now find:

```text
80 → 59610
```

The first matching response appears at:

```text
Packet 9
```

This demonstrates why network engineers must understand conversations rather than simply reading packet numbers sequentially.

---

# Part 39 — Instructor Discussion Question

Ask students:

> Why doesn't the browser simply send an HTTP GET request immediately?

Expected answer:

Because HTTP is using TCP for this connection, and TCP must first establish a reliable transport connection using:

```text
SYN
SYN/ACK
ACK
```

Only after the TCP connection is established can HTTP data be transmitted.

---

# Part 40 — Layer-by-Layer View of Packet 11

When packet 11 carries the HTTP GET request, multiple protocols are involved.

Conceptually:

```text
+--------------------------------+
| Application                    |
| HTTP GET /                     |
+--------------------------------+
| Transport                      |
| TCP                            |
| Src Port 59610 → Dst Port 80   |
+--------------------------------+
| Network                        |
| IPv4                           |
| 192.168.4.100 → 104.16.143.237|
+--------------------------------+
| Data Link                      |
| Ethernet                       |
+--------------------------------+
```

Each layer performs a different job.

---

# Part 41 — Student Knowledge Check

Students should be able to answer these questions without looking at the solutions.

### Question 1

What protocol translates:

```text
www.udemy.com
```

into an IP address?

**Answer:**

```text
DNS
```

---

### Question 2

Which TCP flag normally begins a connection?

**Answer:**

```text
SYN
```

---

### Question 3

What are the three parts of the TCP three-way handshake?

**Answer:**

```text
SYN
SYN/ACK
ACK
```

---

### Question 4

What TCP port normally represents HTTP?

**Answer:**

```text
80
```

---

### Question 5

Which packet begins the TCP connection using source port 59610?

**Answer:**

```text
Packet 5
```

---

### Question 6

Which packet responds to packet 5?

**Answer:**

```text
Packet 9
```

---

### Question 7

Which packet completes that TCP handshake?

**Answer:**

```text
Packet 10
```

---

### Question 8

Why isn't packet 6 the response to packet 5?

**Answer:**

Because packet 6 uses a different client source port.

```text
Packet 5 = 59610 → 80

Packet 6 = 59626 → 80
```

They belong to different TCP conversations.

---

### Question 9

What happens after the TCP connection is established?

**Answer:**

Application data such as HTTP can be transmitted.

In this capture:

```text
Packet 11 = HTTP GET
```

---

### Question 10

What five pieces of information commonly identify a TCP conversation?

**Answer:**

```text
Source IP
Source Port
Destination IP
Destination Port
Protocol
```

This is known as the:

```text
5-Tuple
```

---

# Final Lab Summary

The most important sequence from this capture is:

```text
DNS
 |
 |  Resolve www.udemy.com
 |
 v
104.16.143.237


TCP
 |
 | Packet 5   SYN
 | Packet 9   SYN/ACK
 | Packet 10  ACK
 |
 v
TCP CONNECTION ESTABLISHED


HTTP
 |
 | Packet 11  GET /
 |
 v
WEB SERVER


HTTP RESPONSE
 |
 | Packet 13
 | 301 Moved Permanently
 |
 v
CLIENT
```

Students should leave this lab understanding:

> **DNS finds the server, TCP establishes the connection, and HTTP uses the connection to exchange web data.**

The most important TCP sequence to remember is:

```text
SYN → SYN/ACK → ACK
```

And when analyzing packets in Wireshark, always remember:

> **Do not assume consecutive packet numbers belong to the same conversation. Check the IP addresses, TCP ports, protocol, and TCP stream.**