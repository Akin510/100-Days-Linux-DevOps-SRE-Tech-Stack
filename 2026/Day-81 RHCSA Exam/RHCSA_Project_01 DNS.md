# Cisco Packet Tracer Lab: DNS, ARP, and ICMP

> **Objective:** Build a simple LAN in Cisco Packet Tracer, configure a
> DNS server, and observe how **ARP, DNS, and ICMP** work together when
> a client pings a server by hostname.

------------------------------------------------------------------------

# 🎯 Learning Objectives

By the end of this lab, students will be able to:

-   Build and cable a simple switched LAN.
-   Configure static IPv4 addresses.
-   Add and configure a Packet Tracer DNS server.
-   Create a DNS **A record**.
-   Configure a PC to use a DNS server.
-   Test connectivity by IP address.
-   Test DNS name resolution by hostname.
-   Understand the difference between DNS, ARP, and ICMP.
-   Observe ARP, DNS, and ICMP in Packet Tracer Simulation Mode.
-   Explain why, with an empty ARP cache, ARP may occur before the DNS
    query is sent.

------------------------------------------------------------------------

# 1. Network Topology

Keep the existing four PCs and add one **Server-PT** to the same switch.

Use the following addressing:

  Device               IP Address       Subnet Mask       DNS Server
  -------------------- ---------------- ----------------- ----------------
  **PC0**              `192.168.1.10`   `255.255.255.0`   `192.168.1.50`
  **PC2**              `192.168.1.20`   `255.255.255.0`   Optional
  **PC1**              `192.168.1.30`   `255.255.255.0`   Optional
  **PC3**              `192.168.1.40`   `255.255.255.0`   Optional
  **DNS/Web Server**   `192.168.1.50`   `255.255.255.0`   N/A

All devices belong to:

``` text
192.168.1.0/24
```

For this same-LAN lab, a default gateway is not required.

## Topology Pictorial

``` text
                       192.168.1.0/24

       PC0                                      PC2
  192.168.1.10                             192.168.1.20
        │                                        │
        │                                        │
        └──────────────┐          ┌──────────────┘
                       │          │
                    ┌──┴──────────┴──┐
                    │     Switch     │
                    └──┬──────┬───┬──┘
                       │      │   │
          ┌────────────┘      │   └─────────────┐
          │                   │                 │
         PC1                 PC3          DNS/Web Server
    192.168.1.30        192.168.1.40       192.168.1.50
```

------------------------------------------------------------------------

# 2. Cabling the Topology

Use **Copper Straight-Through** cables between the PCs/server and the
switch.

Example:

  From        Interface       To       Interface   Cable
  ----------- --------------- -------- ----------- -------------------------
  PC0         FastEthernet0   Switch   Fa0/1       Copper Straight-Through
  PC2         FastEthernet0   Switch   Fa0/2       Copper Straight-Through
  PC1         FastEthernet0   Switch   Fa0/3       Copper Straight-Through
  PC3         FastEthernet0   Switch   Fa0/4       Copper Straight-Through
  Server-PT   FastEthernet0   Switch   Fa0/5       Copper Straight-Through

> The exact switch port numbers are not important. Any available access
> ports can be used.

------------------------------------------------------------------------

# 3. Configure the Existing PCs

Configure the PCs from:

``` text
PC → Desktop → IP Configuration
```

## PC0

``` text
IP Address:       192.168.1.10
Subnet Mask:      255.255.255.0
DNS Server:       192.168.1.50
```

## PC2

``` text
IP Address:       192.168.1.20
Subnet Mask:      255.255.255.0
```

## PC1

``` text
IP Address:       192.168.1.30
Subnet Mask:      255.255.255.0
```

## PC3

``` text
IP Address:       192.168.1.40
Subnet Mask:      255.255.255.0
```

------------------------------------------------------------------------

# 4. Add the DNS Server

In Packet Tracer, select:

``` text
End Devices
    ↓
Server
    ↓
Server-PT
```

Drag the server onto the workspace and connect it to the switch using a
**Copper Straight-Through** cable.

------------------------------------------------------------------------

# 5. Configure the Server IP Address

Click:

``` text
Server
   ↓
Desktop
   ↓
IP Configuration
```

Configure:

``` text
IP Address:       192.168.1.50
Subnet Mask:      255.255.255.0
Default Gateway:  Leave blank
```

Because all devices are on `192.168.1.0/24`, they can communicate
directly without a router.

------------------------------------------------------------------------

# 6. Test Basic IP Connectivity

Before configuring DNS, verify that PC0 can reach the server by IP
address.

Open:

``` text
PC0 → Desktop → Command Prompt
```

Run:

``` cmd
ping 192.168.1.50
```

A successful ping proves that basic Layer 3 connectivity between PC0 and
the server is working.

This is an important troubleshooting rule:

``` text
Test IP connectivity first
          ↓
Then test name resolution
```

If:

``` cmd
ping 192.168.1.50
```

works, but:

``` cmd
ping www.nitacademy.local
```

does not, investigate the DNS configuration.

------------------------------------------------------------------------

# 7. Enable DNS on the Server

Click:

``` text
Server
   ↓
Services
   ↓
DNS
```

Set:

``` text
DNS: ON
```

------------------------------------------------------------------------

# 8. Create the DNS Record

Create the following record:

``` text
Name:     www.nitacademy.local
Address:  192.168.1.50
```

Click **Add**.

The DNS server now contains the mapping:

``` text
www.nitacademy.local
          ↓
     192.168.1.50
```

This is an **A record** because it maps a hostname to an IPv4 address.

------------------------------------------------------------------------

# 9. Verify PC0's DNS Configuration

On PC0, return to:

``` text
PC0
 ↓
Desktop
 ↓
IP Configuration
```

Verify:

``` text
IP Address:       192.168.1.10
Subnet Mask:      255.255.255.0
DNS Server:       192.168.1.50
```

The important setting is:

``` text
DNS Server: 192.168.1.50
```

This tells PC0 where to send DNS queries.

------------------------------------------------------------------------

# 10. Enable HTTP on the Server

For an additional demonstration, enable HTTP on the same Server-PT.

Click:

``` text
Server
   ↓
Services
   ↓
HTTP
```

Set:

``` text
HTTP: ON
```

The server now performs two roles:

``` text
                Server
             192.168.1.50
                   │
          ┌────────┴────────┐
          │                 │
          ▼                 ▼
         DNS               HTTP
      Service            Service
      Port 53            Port 80
```

------------------------------------------------------------------------

# 11. Test DNS Name Resolution

From PC0 run:

``` cmd
ping www.nitacademy.local
```

If DNS is working, PC0 should resolve:

``` text
www.nitacademy.local
          ↓
     192.168.1.50
```

and then ping `192.168.1.50`.

------------------------------------------------------------------------

# 12. Test the Web Server by Name

On PC0 open:

``` text
Desktop
   ↓
Web Browser
```

Enter:

``` text
http://www.nitacademy.local
```

The process is:

``` text
www.nitacademy.local
          │
          │ DNS
          ▼
     192.168.1.50
          │
          │ HTTP
          ▼
       Web Server
```

If DNS and HTTP are configured correctly, the server's web page should
appear.

------------------------------------------------------------------------

# 13. Observe the Traffic in Simulation Mode

Switch Packet Tracer from:

``` text
Realtime
```

to:

``` text
Simulation
```

Under **Event List Filters**, select only:

``` text
ARP
DNS
ICMP
```

Then run from PC0:

``` cmd
ping www.nitacademy.local
```

Use **Capture/Forward** to observe the packets one step at a time.

------------------------------------------------------------------------

# 14. Correct DNS Test Sequence

Assuming PC0 begins without the DNS server's MAC address in its ARP
cache, the sequence is:

``` text
ARP Request
     ↓
ARP Reply
     ↓
DNS Query
     ↓
DNS Reply
     ↓
ICMP Echo Request
     ↓
ICMP Echo Reply
```

The reason ARP can occur **before** the DNS query is that PC0 already
knows the IP address of its configured DNS server:

``` text
DNS Server = 192.168.1.50
```

However, because the DNS server is on the local Ethernet LAN, PC0 needs
the server's MAC address before it can deliver the DNS query.

------------------------------------------------------------------------

# 15. Complete ARP → DNS → ICMP Flow

When the student enters:

``` cmd
ping www.nitacademy.local
```

the complete clean-cache flow is:

``` text
PC0
192.168.1.10
      │
      │ User enters:
      │ ping www.nitacademy.local
      │
      ▼
PC0 needs to contact its configured
DNS server: 192.168.1.50
      │
      ▼
Does PC0 know the MAC address
for 192.168.1.50?
      │
     NO
      │
      ▼
① ARP REQUEST
"Who has 192.168.1.50?"
      │
      ▼
DNS Server
192.168.1.50
      │
      ▼
② ARP REPLY
"192.168.1.50 is at my MAC address"
      │
      ▼
PC0 stores:
192.168.1.50 → Server MAC
      │
      ▼
③ DNS QUERY
"What IPv4 address belongs to
www.nitacademy.local?"
      │
      ▼
DNS Server checks its DNS record
      │
      ▼
④ DNS REPLY
www.nitacademy.local
        =
192.168.1.50
      │
      ▼
PC0 now knows the destination IP
      │
      ▼
⑤ ICMP ECHO REQUEST
PC0 → 192.168.1.50
      │
      ▼
⑥ ICMP ECHO REPLY
192.168.1.50 → PC0
      │
      ▼
PING SUCCESSFUL
```

------------------------------------------------------------------------

# 16. Why There Is Normally No Second ARP

In this particular lab, the DNS server and the hostname being pinged
point to the **same machine**:

``` text
DNS Server:
192.168.1.50

www.nitacademy.local:
192.168.1.50
```

PC0 already learned the MAC address for `192.168.1.50` before sending
the DNS query.

Therefore, after receiving the DNS reply, PC0 can normally send the ICMP
Echo Request using the ARP information it already learned.

The flow remains:

``` text
ARP
 ↓
DNS
 ↓
ICMP
```

rather than:

``` text
ARP
 ↓
DNS
 ↓
ARP again
 ↓
ICMP
```

------------------------------------------------------------------------

# 17. Understand What Each Protocol Does

  ---------------------------------------------------------------------------------------
  Protocol                Purpose                 Example in This Lab
  ----------------------- ----------------------- ---------------------------------------
  **ARP**                 Resolve a local IPv4    `192.168.1.50 → Server MAC`
                          address to a MAC        
                          address                 

  **DNS**                 Resolve a hostname to   `www.nitacademy.local → 192.168.1.50`
                          an IP address           

  **ICMP**                Test/report IP          Echo Request / Echo Reply
                          reachability            

  **HTTP**                Deliver web content     Browser → `www.nitacademy.local`
  ---------------------------------------------------------------------------------------

The simplest memory rule is:

``` text
DNS  = Name → IP

ARP  = Local IPv4 → MAC

ICMP = Test IP reachability
```

------------------------------------------------------------------------

# 18. Important Note About Caching

If you repeat:

``` cmd
ping www.nitacademy.local
```

you may not see exactly the same packet sequence.

For example, PC0 may already have:

``` text
192.168.1.50 → Server MAC
```

stored in its ARP cache.

If the MAC address is already known, another ARP exchange is
unnecessary.

The observed sequence may therefore begin with:

``` text
DNS Query
     ↓
DNS Reply
     ↓
ICMP Echo Request
     ↓
ICMP Echo Reply
```

> **Teaching Point:** ARP occurs when a device needs a local MAC address
> that it does not already know or have cached.

------------------------------------------------------------------------

# 19. Troubleshooting Flow

Use the following troubleshooting sequence:

``` text
Can PC0 ping 192.168.1.50?
            │
       ┌────┴────┐
       │         │
      YES        NO
       │         │
       │         ▼
       │    Fix basic IP
       │    connectivity
       │
       ▼
Can PC0 ping
www.nitacademy.local?
            │
       ┌────┴────┐
       │         │
      YES        NO
       │         │
       ▼         ▼
     DNS       Check:
     Works     - PC0 DNS setting
               - DNS service ON
               - DNS record
```

If the IP address works but the hostname does not, verify:

``` text
PC0 DNS Server:
192.168.1.50
```

Then verify:

``` text
Server → Services → DNS → ON
```

Finally verify the DNS record:

``` text
www.nitacademy.local → 192.168.1.50
```

------------------------------------------------------------------------

# 20. Student Verification Checklist

Students should complete all of the following tests.

## Test 1 -- IP Connectivity

``` cmd
ping 192.168.1.50
```

Expected result:

``` text
SUCCESS
```

## Test 2 -- DNS Resolution

``` cmd
ping www.nitacademy.local
```

Expected resolution:

``` text
www.nitacademy.local
        ↓
192.168.1.50
```

followed by successful ICMP replies.

## Test 3 -- Web Access

Open the browser and enter:

``` text
http://www.nitacademy.local
```

The server's HTTP page should appear.

## Test 4 -- Simulation Mode

Filter for:

``` text
ARP
DNS
ICMP
```

Run:

``` cmd
ping www.nitacademy.local
```

Observe the protocol flow.

------------------------------------------------------------------------

# 🧪 Knowledge Check

### Question 1

What does DNS resolve?

**Answer:**

``` text
Hostname → IP Address
```

### Question 2

What does ARP resolve?

**Answer:**

``` text
Local IPv4 Address → MAC Address
```

### Question 3

What DNS server is PC0 using?

**Answer:**

``` text
192.168.1.50
```

### Question 4

What IP address does `www.nitacademy.local` resolve to?

**Answer:**

``` text
192.168.1.50
```

### Question 5

Why can ARP occur before the DNS query?

**Answer:**

PC0 knows the DNS server's IP address, but it may first need to learn
the DNS server's MAC address to deliver the DNS query across the local
Ethernet LAN.

### Question 6

Why is another ARP request normally unnecessary after the DNS reply in
this lab?

**Answer:**

The DNS server and `www.nitacademy.local` both use `192.168.1.50`, so
PC0 already learned the required MAC address while contacting the DNS
server.

------------------------------------------------------------------------

# 📌 Quick Revision

  Item                   What Students Should Know
  ---------------------- ---------------------------------------
  Network                `192.168.1.0/24`
  PC0                    `192.168.1.10`
  DNS Server             `192.168.1.50`
  DNS Record             `www.nitacademy.local → 192.168.1.50`
  DNS                    Name → IP
  ARP                    Local IPv4 → MAC
  ICMP                   Tests/reports IP reachability
  HTTP                   Provides web service
  Clean-cache sequence   ARP → DNS → ICMP
  Simulation filters     ARP, DNS, ICMP

------------------------------------------------------------------------

# 🔑 Key Takeaways

-   All devices in this lab are on `192.168.1.0/24`.
-   The DNS server uses `192.168.1.50`.
-   PC0 is configured to use `192.168.1.50` as its DNS server.
-   The DNS A record maps `www.nitacademy.local` to `192.168.1.50`.
-   DNS translates a hostname into an IP address.
-   ARP resolves the local IPv4 address needed for Ethernet delivery to
    a MAC address.
-   ICMP provides the Echo Request/Echo Reply exchange used by `ping`.
-   With an empty ARP cache, PC0 may need ARP before it can deliver the
    DNS query.
-   Because the DNS server and ping destination are the same host in
    this lab, another ARP exchange is normally unnecessary before ICMP.
-   Cached ARP information can change what students see during repeated
    Simulation Mode tests.

------------------------------------------------------------------------

# ⭐ Golden Rule

``` text
DNS
NAME → IP

ARP
LOCAL IPv4 → MAC

ICMP
TEST IP REACHABILITY
```

For this specific lab with a clean ARP cache:

``` text
ARP → DNS → ICMP
```

### Complete Student Memory Flow

``` text
ping www.nitacademy.local
            │
            ▼
Need DNS server
192.168.1.50
            │
            ▼
Need its MAC
            │
            ▼
           ARP
            │
            ▼
Send DNS Query
            │
            ▼
Receive DNS Reply
            │
            ▼
www.nitacademy.local
=
192.168.1.50
            │
            ▼
Send ICMP Echo Request
            │
            ▼
Receive ICMP Echo Reply
            │
            ▼
       PING SUCCESS
```
