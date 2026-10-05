# 16. Correct DNS Test Sequence in Simulation Mode

For the clearest demonstration, switch Packet Tracer from:

```text
Realtime
```

to:

```text
Simulation
```

Under **Event List Filters**, select only:

```text
ARP
DNS
ICMP
```

Then from PC0 run:

```cmd
ping www.nitacademy.local
```

Assuming PC0 starts with an empty ARP cache and no cached DNS result, the expected sequence is:

```text
1. ARP Request
       ↓
2. ARP Reply
       ↓
3. DNS Query
       ↓
4. DNS Reply
       ↓
5. ICMP Echo Request
       ↓
6. ICMP Echo Reply
```

---

# 17. Why ARP Happens Before DNS

PC0 has been configured with:

```text
DNS Server = 192.168.1.50
```

When the user enters:

```cmd
ping www.nitacademy.local
```

PC0 knows **which DNS server IP address to contact**, but Ethernet communication requires a destination MAC address.

Because:

```text
PC0       = 192.168.1.10/24
DNS Server = 192.168.1.50/24
```

both devices are on:

```text
192.168.1.0/24
```

Therefore, PC0 must learn the DNS server's MAC address before it can send the DNS query.

---

# 18. Step 1 – ARP Request

PC0 broadcasts an ARP request:

```text
PC0
192.168.1.10
      │
      │ ARP Request
      │
      │ "Who has 192.168.1.50?"
      │
      ▼
   Switch
      │
      ├──────────────► Other LAN devices
      │
      └──────────────► DNS Server
                       192.168.1.50
```

The Ethernet destination for the ARP request is:

```text
FF:FF:FF:FF:FF:FF
```

which is the Ethernet broadcast MAC address.

---

# 19. Step 2 – ARP Reply

The DNS server recognizes:

```text
192.168.1.50
```

as its own IP address.

It sends an ARP reply to PC0:

```text
PC0                                DNS Server
192.168.1.10                       192.168.1.50
      │                                  │
      │   Who has 192.168.1.50?          │
      │─────────────────────────────────►│
      │                                  │
      │   192.168.1.50 is at my MAC      │
      │◄─────────────────────────────────│
```

PC0 now stores a mapping similar to:

```text
192.168.1.50 → DNS-Server-MAC
```

in its ARP cache.

At this point, PC0 can physically deliver Ethernet frames to the DNS server.

---

# 20. Step 3 – DNS Query

PC0 can now send the DNS query.

Conceptually:

```text
PC0
192.168.1.10
      │
      │ DNS Query:
      │
      │ "What is the IPv4 address of
      │  www.nitacademy.local?"
      │
      ▼
DNS Server
192.168.1.50
```

The DNS query is carried inside an IP packet and Ethernet frame.

Conceptually:

```text
Ethernet Frame
┌────────────────────────────────────┐
│ Source MAC:      PC0 MAC           │
│ Destination MAC: DNS Server MAC    │
├────────────────────────────────────┤
│ IP Packet                          │
│ Source IP:       192.168.1.10      │
│ Destination IP:  192.168.1.50      │
├────────────────────────────────────┤
│ DNS Query                          │
│ www.nitacademy.local ?             │
└────────────────────────────────────┘
```

---

# 21. Step 4 – DNS Reply

The DNS server looks at its DNS records and finds:

```text
www.nitacademy.local
        ↓
192.168.1.50
```

It sends the answer back to PC0:

```text
DNS Server
192.168.1.50
      │
      │ DNS Reply
      │
      │ "www.nitacademy.local
      │  = 192.168.1.50"
      │
      ▼
PC0
192.168.1.10
```

Now PC0 has resolved:

```text
HOSTNAME
www.nitacademy.local
        │
        │ DNS
        ▼
IP ADDRESS
192.168.1.50
```

---

# 22. Does PC0 Need ARP Again?

Normally, **no**.

This is an important part of this particular lab.

PC0 just communicated with the DNS server at:

```text
192.168.1.50
```

and therefore already learned:

```text
192.168.1.50 → DNS-Server-MAC
```

The DNS answer also tells PC0:

```text
www.nitacademy.local → 192.168.1.50
```

Notice that the DNS server and the ping destination are the **same machine**:

```text
DNS Server IP:
192.168.1.50

www.nitacademy.local:
192.168.1.50
```

Therefore, PC0 normally already has the required MAC address in its ARP cache.

It can proceed directly to ICMP.

---

# 23. Step 5 – ICMP Echo Request

PC0 now sends:

```text
ICMP Echo Request
```

to:

```text
192.168.1.50
```

Conceptually:

```text
PC0
192.168.1.10
      │
      │ ICMP Echo Request
      │
      ▼
Server
192.168.1.50
```

The Ethernet frame uses the MAC address PC0 already learned during the earlier ARP exchange.

---

# 24. Step 6 – ICMP Echo Reply

The server receives the ICMP Echo Request and responds:

```text
ICMP Echo Reply
```

Conceptually:

```text
PC0                                Server
192.168.1.10                    192.168.1.50
      │                               │
      │       Echo Request            │
      │──────────────────────────────►│
      │                               │
      │       Echo Reply              │
      │◄──────────────────────────────│
```

The ping succeeds.

---

# 25. Correct Complete Packet Sequence

For this specific lab:

```text
PC0
192.168.1.10
      │
      │
      │ ① ARP Request
      │ "Who has 192.168.1.50?"
      ▼
DNS Server
192.168.1.50
      │
      │ ② ARP Reply
      │ "192.168.1.50 is at my MAC"
      ▼
PC0
      │
      │ ③ DNS Query
      │ "What is www.nitacademy.local?"
      ▼
DNS Server
      │
      │ ④ DNS Reply
      │ "www.nitacademy.local
      │  = 192.168.1.50"
      ▼
PC0
      │
      │ ⑤ ICMP Echo Request
      ▼
Server
      │
      │ ⑥ ICMP Echo Reply
      ▼
PC0
```

So the clean teaching sequence is:

```text
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

---

# 26. What Each Protocol Did

| Stage | Protocol | Question Being Answered |
|---|---|---|
| 1 | **ARP** | What MAC address belongs to `192.168.1.50`? |
| 2 | **DNS** | What IP address belongs to `www.nitacademy.local`? |
| 3 | **ICMP** | Can I reach `192.168.1.50`? |

This gives students three very different resolution/communication concepts:

```text
DNS
Hostname → IP Address

ARP
Local IPv4 Address → MAC Address

ICMP
IP Reachability Test
```

---

# 27. Very Important Teaching Point

Do **not** teach the sequence as:

```text
DNS
 ↓
ARP
 ↓
ICMP
```

for this clean-cache same-LAN example.

The DNS query itself must first be transported across Ethernet.

PC0 already knows:

```text
DNS Server IP = 192.168.1.50
```

but if it does not know the corresponding MAC address, it needs ARP **before it can send the DNS query**.

Therefore:

```text
Need to contact DNS server
           │
           ▼
Know DNS server IP?
           │
          YES
           │
           ▼
Know its MAC?
       /       \
     NO         YES
      │          │
      ▼          │
     ARP         │
      │          │
      └────┬─────┘
           ▼
       DNS Query
           │
           ▼
       DNS Reply
           │
           ▼
      Name Resolved
           │
           ▼
         ICMP
```

---

# 28. Why Students May Not Always See ARP

If you repeat:

```cmd
ping www.nitacademy.local
```

Packet Tracer may not show the exact same sequence.

For example, PC0 may already know:

```text
192.168.1.50 → Server MAC
```

from its ARP cache.

In that case, another ARP request is unnecessary.

The observed traffic may begin with:

```text
DNS Query
     ↓
DNS Reply
     ↓
ICMP Echo Request
     ↓
ICMP Echo Reply
```

Similarly, cached information can affect what students observe.

For classroom demonstrations, remember:

> **ARP happens when a device needs a local MAC address that it does not already know.**

---

# ⭐ Correct Teaching Flow

For a clean first attempt:

```text
www.nitacademy.local
        │
        │
        ▼
PC0 knows its configured
DNS server is 192.168.1.50
        │
        ▼
Need MAC for 192.168.1.50
        │
        ▼
       ARP
        │
        ▼
DNS Server MAC learned
        │
        ▼
    DNS Query
        │
        ▼
    DNS Reply
        │
        ▼
www.nitacademy.local
        =
   192.168.1.50
        │
        ▼
ICMP Echo Request
        │
        ▼
ICMP Echo Reply
```

# ⭐ Golden Rule

```text
DNS = "What IP belongs to this NAME?"

ARP = "What MAC do I use for this LOCAL IP?"

ICMP = "Can I reach this IP?"
```

For this specific Packet Tracer lab, with an empty ARP cache:

```text
ARP → DNS → ICMP
```

because **PC0 must first be able to deliver the DNS query to the DNS server at Layer 2**.