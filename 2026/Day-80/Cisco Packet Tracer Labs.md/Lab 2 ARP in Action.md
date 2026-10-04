# Cisco Packet Tracer Lab: Observing ARP in Action

This lab demonstrates how **Address Resolution Protocol (ARP)** operates both within a single **Local Area Network (LAN)** and across router hops connecting different subnets.

---

# 🎯 Lab Objectives

By the end of this lab, students will be able to:

- Build and cable a two-LAN routed topology in Cisco Packet Tracer.
- Configure IPv4 addresses and default gateways.
- Observe how ARP resolves an IPv4 address to a MAC address.
- Examine ARP tables on PCs and routers.
- Understand ARP behavior within the same subnet.
- Understand ARP behavior when communicating with a different subnet.
- Observe that a PC ARPs for its **default gateway** when the destination is remote.
- Observe ARP occurring independently on each Ethernet segment.
- Understand that routers rebuild the **Layer 2 Ethernet frame** at each routed hop.
- Use Cisco Packet Tracer **Simulation Mode** to observe ARP and ICMP packets.

---

# 1. Required Devices

Place the following devices into the Packet Tracer workspace:

| Device Type | Quantity | Suggested Device |
|---|---:|---|
| PC | 3 | PC-PT |
| Switch | 2 | Cisco 2960 |
| Router | 2 | Router with at least two Gigabit Ethernet interfaces |
| Ethernet Links | 5 | Copper Straight-Through |

You should have:

```text
3 PCs
2 Switches
2 Routers
5 Ethernet Cables
```

---

# 2. Physical Topology

Build the network in the following order:

```text
                         LAN 1
                   192.168.1.0/24

       PC0                                  PC2
192.168.1.10/24                      192.168.1.20/24
       │                                    │
       │                                    │
       └──────────────┐      ┌──────────────┘
                      ▼      ▼
                    ┌──────────┐
                    │ Switch0  │
                    │   2960   │
                    └────┬─────┘
                         │
                         │
                    Gi0/0/0
                  192.168.1.1
                    ┌─────────┐
                    │ Router0 │
                    └────┬────┘
                    Gi0/0/1
                     10.0.0.1
                         │
                         │
                   10.0.0.0/24
                         │
                     10.0.0.2
                    Gi0/0/1
                    ┌─────────┐
                    │ Router1 │
                    └────┬────┘
                    Gi0/0/0
                  192.168.2.1
                         │
                         │
                    ┌────┴─────┐
                    │ Switch1  │
                    │   2960   │
                    └────┬─────┘
                         │
                         │
                        PC1
                  192.168.2.10/24

                         LAN 2
                   192.168.2.0/24
```

---

# 3. Understand the Three Networks

Before cabling the devices, students should recognize that this topology contains **three separate IPv4 networks**:

```text
LAN 1
192.168.1.0/24
      │
      ▼
PC0 + PC2 + Router0
```

```text
Router-to-Router Network
10.0.0.0/24
      │
      ▼
Router0 + Router1
```

```text
LAN 2
192.168.2.0/24
      │
      ▼
Router1 + PC1
```

The routers connect these three networks together.

---

# 4. Cabling Diagram

Use the following connections:

```text
PC0
FastEthernet0
     │
     │ Copper Straight-Through
     ▼
Switch0
Fa0/1


PC2
FastEthernet0
     │
     │ Copper Straight-Through
     ▼
Switch0
Fa0/2


Switch0
Gi0/1
     │
     │ Copper Straight-Through
     ▼
Router0
Gi0/0/0


Router0
Gi0/0/1
     │
     │ Ethernet Link
     ▼
Router1
Gi0/0/1


Router1
Gi0/0/0
     │
     │ Copper Straight-Through
     ▼
Switch1
Gi0/1


Switch1
Fa0/1
     │
     │ Copper Straight-Through
     ▼
PC1
FastEthernet0
```

> **Note:** Exact switch port numbers are not important. If `Fa0/1` or `Gi0/1` is already being used, another available Ethernet port can be selected.

---

# 5. Cabling Table

| From Device | From Interface | To Device | To Interface | Cable |
|---|---|---|---|---|
| **PC0** | FastEthernet0 | **Switch0** | Fa0/1 | Copper Straight-Through |
| **PC2** | FastEthernet0 | **Switch0** | Fa0/2 | Copper Straight-Through |
| **Switch0** | Gi0/1 | **Router0** | Gi0/0/0 | Copper Straight-Through |
| **Router0** | Gi0/0/1 | **Router1** | Gi0/0/1 | Ethernet connection* |
| **Router1** | Gi0/0/0 | **Switch1** | Gi0/1 | Copper Straight-Through |
| **Switch1** | Fa0/1 | **PC1** | FastEthernet0 | Copper Straight-Through |

> **\*Router-to-Router Cable:** In modern Packet Tracer topologies, **Copper Straight-Through** can normally be used, especially when Auto-MDIX is supported. Packet Tracer's **Automatically Choose Connection Type** option is also perfectly acceptable for this lab.

---

# 6. Step-by-Step Packet Tracer Topology Build

## Step 1 – Place the PCs

From:

**End Devices → PC**

place three PCs on the workspace.

Rename them:

```text
PC0
PC2
PC1
```

Arrange:

```text
PC0        PC2                              PC1
```

PC0 and PC2 will belong to **LAN1**.

PC1 will belong to **LAN2**.

---

## Step 2 – Place the Switches

From:

**Network Devices → Switches**

place two Cisco 2960 switches.

Rename them:

```text
Switch0
Switch1
```

Position:

```text
PC0 ──┐
      ├── Switch0
PC2 ──┘


                    Switch1 ─── PC1
```

---

## Step 3 – Place the Routers

From:

**Network Devices → Routers**

place two routers between the switches.

Rename them:

```text
Router0
Router1
```

The basic arrangement should now be:

```text
PC0 ──┐
      │
PC2 ──┴── Switch0 ─── Router0 ─── Router1 ─── Switch1 ─── PC1
```

---

## Step 4 – Cable PC0 to Switch0

Select:

**Connections → Copper Straight-Through**

Connect:

```text
PC0 FastEthernet0
        │
        ▼
Switch0 Fa0/1
```

---

## Step 5 – Cable PC2 to Switch0

Connect:

```text
PC2 FastEthernet0
        │
        ▼
Switch0 Fa0/2
```

LAN1 is beginning to take shape:

```text
PC0 ─────┐
         ├──── Switch0
PC2 ─────┘
```

---

## Step 6 – Cable Switch0 to Router0

Connect:

```text
Switch0 Gi0/1
      │
      ▼
Router0 Gi0/0/0
```

This router interface will later receive:

```text
192.168.1.1/24
```

and become the **default gateway for LAN1**.

---

## Step 7 – Cable Router0 to Router1

Connect:

```text
Router0 Gi0/0/1
        │
        ▼
Router1 Gi0/0/1
```

These interfaces will use:

```text
Router0 = 10.0.0.1/24

Router1 = 10.0.0.2/24
```

This creates the transit network:

```text
10.0.0.0/24
```

---

## Step 8 – Cable Router1 to Switch1

Connect:

```text
Router1 Gi0/0/0
      │
      ▼
Switch1 Gi0/1
```

Router1's interface will later receive:

```text
192.168.2.1/24
```

and become the **default gateway for LAN2**.

---

## Step 9 – Cable Switch1 to PC1

Connect:

```text
Switch1 Fa0/1
       │
       ▼
PC1 FastEthernet0
```

The complete physical topology is now:

```text
 PC0 ─────┐
          │
          ├── Switch0 ─── Router0 ─── Router1 ─── Switch1 ─── PC1
          │
 PC2 ─────┘
```

---

# 7. Label the Topology

Before configuring anything, label the networks in Packet Tracer.

Use the **Place Note** tool and add:

```text
LAN1
192.168.1.0/24
```

next to PC0, PC2, and Switch0.

Between the routers add:

```text
Transit Network
10.0.0.0/24
```

Next to Switch1 and PC1 add:

```text
LAN2
192.168.2.0/24
```

The topology should visually communicate:

```text
┌─────────────────────────────┐
│ LAN1 – 192.168.1.0/24       │
│                             │
│ PC0 ──┐                     │
│       ├── Switch0 ── Router0│
│ PC2 ──┘                     │
└──────────────────────┬──────┘
                       │
                10.0.0.0/24
                       │
                       ▼
                    Router1
                       │
┌──────────────────────┴──────┐
│ LAN2 – 192.168.2.0/24       │
│                             │
│       Switch1 ───── PC1     │
│                             │
└─────────────────────────────┘
```

---

# 8. Cabling Verification

Before assigning IP addresses, verify the physical topology.

The links may initially appear:

```text
RED
```

on router interfaces.

This is expected because Cisco router interfaces are commonly administratively shut down until:

```cisco
no shutdown
```

is configured.

After configuring the router interfaces, the links should transition toward:

```text
GREEN = Link Up
```

---

# 9. IP Address Planning & Setup Table

Now that the physical topology is complete, assign the following addresses:

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| **PC0** | FastEthernet0 | `192.168.1.10` | `255.255.255.0` | `192.168.1.1` |
| **PC2** | FastEthernet0 | `192.168.1.20` | `255.255.255.0` | `192.168.1.1` |
| **Router0** | GigabitEthernet0/0/0 *(LAN1)* | `192.168.1.1` | `255.255.255.0` | N/A |
| **Router0** | GigabitEthernet0/0/1 *(Transit)* | `10.0.0.1` | `255.255.255.0` | N/A |
| **Router1** | GigabitEthernet0/0/1 *(Transit)* | `10.0.0.2` | `255.255.255.0` | N/A |
| **Router1** | GigabitEthernet0/0/0 *(LAN2)* | `192.168.2.1` | `255.255.255.0` | N/A |
| **PC1** | FastEthernet0 | `192.168.2.10` | `255.255.255.0` | `192.168.2.1` |

---

# 10. Addressing Pictorial

Students should be able to look at the topology and understand **why each address is assigned**:

```text
                        LAN1
                  192.168.1.0/24

  PC0                                     PC2
192.168.1.10                           192.168.1.20
GW: 192.168.1.1                       GW: 192.168.1.1
   │                                       │
   └────────────── Switch0 ────────────────┘
                       │
                       │
                Router0 Gi0/0/0
                  192.168.1.1
                       │
                Router0 Gi0/0/1
                    10.0.0.1
                       │
                       │
                  10.0.0.0/24
                       │
                       │
                    10.0.0.2
                Router1 Gi0/0/1
                       │
                Router1 Gi0/0/0
                  192.168.2.1
                       │
                    Switch1
                       │
                       │
                      PC1
                 192.168.2.10
                 GW: 192.168.2.1

                        LAN2
                  192.168.2.0/24
```

---

# 11. Pre-Configuration Student Check

Before students continue, have them answer:

```text
How many networks are present?
→ 3

What is LAN1?
→ 192.168.1.0/24

What is the router transit network?
→ 10.0.0.0/24

What is LAN2?
→ 192.168.2.0/24

What is PC0's gateway?
→ 192.168.1.1

What is PC1's gateway?
→ 192.168.2.1

Which devices are in the same subnet as PC0?
→ PC2 and Router0 Gi0/0/0

Is PC1 in PC0's subnet?
→ No
```

This understanding is essential because it determines **which IP address PC0 will ARP for** later in the lab.

---

# 12. Configure IP Addresses on the PCs

For each PC:

**Click PC → Desktop → IP Configuration**

### PC0

```text
IP Address:       192.168.1.10
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.1.1
```

### PC2

```text
IP Address:       192.168.1.20
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.1.1
```

### PC1

```text
IP Address:       192.168.2.10
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.2.1
```

---

# 13. Configure Router0

Click:

**Router0 → CLI**

```cisco
enable
configure terminal

interface GigabitEthernet0/0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/0/1
 ip address 10.0.0.1 255.255.255.0
 no shutdown
exit

ip route 192.168.2.0 255.255.255.0 10.0.0.2

end
```

---

# 14. Configure Router1

Click:

**Router1 → CLI**

```cisco
enable
configure terminal

interface GigabitEthernet0/0/0
 ip address 192.168.2.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/0/1
 ip address 10.0.0.2 255.255.255.0
 no shutdown
exit

ip route 192.168.1.0 255.255.255.0 10.0.0.1

end
```

---

# 15. Verify Router Interfaces

On both routers:

```cisco
show ip interface brief
```

Look for:

```text
Status      Protocol
up          up
```

Then:

```cisco
show ip route
```

Finally:

```cisco
show ip arp
```

---

# 16. Part 1 – Observe ARP Within the Same LAN

We will first ping:

```text
PC0 → PC2
```

Both belong to:

```text
192.168.1.0/24
```

Therefore, they are on the **same subnet**.

Switch Packet Tracer to **Simulation Mode** and filter for:

```text
ARP
ICMP
```

From PC0:

```cmd
ping 192.168.1.20
```

PC0 determines:

```text
192.168.1.10/24
        │
        ├── Same network
        │
192.168.1.20/24
```

Therefore PC0 asks:

```text
Who has 192.168.1.20?
```

The ARP request is broadcast using:

```text
FF:FF:FF:FF:FF:FF
```

PC2 replies with its MAC address.

PC0 stores:

```text
192.168.1.20 → PC2 MAC
```

and sends the ICMP Echo Request.

Verify:

```cmd
arp -a
```

---

# 17. Part 2 – Observe ARP Across Routers

Now ping:

```text
PC0 → PC1
```

From PC0:

```cmd
ping 192.168.2.10
```

PC0 compares:

```text
192.168.1.10/24 → 192.168.1.0/24

192.168.2.10/24 → 192.168.2.0/24
```

The networks are different.

Therefore PC0 does **not** ARP for PC1.

It ARPs for:

```text
192.168.1.1
```

which is its default gateway.

---

# 18. Complete ARP Flow

```text
PC0
192.168.1.10
 │
 │ ARP:
 │ Who has 192.168.1.1?
 ▼
Router0
192.168.1.1
 │
 │ Route Lookup
 │
 │ ARP:
 │ Who has 10.0.0.2?
 ▼
Router1
10.0.0.2
 │
 │ Route Lookup
 │
 │ ARP:
 │ Who has 192.168.2.10?
 ▼
PC1
192.168.2.10
```

This is the key lesson:

> **ARP does not travel from PC0 through the routers to PC1. Each Ethernet segment performs its own ARP resolution.**

---

# 19. Layer 2 vs Layer 3

### Hop 1

```text
PC0 → Router0

Source MAC      = PC0
Destination MAC = Router0

Source IP       = 192.168.1.10
Destination IP  = 192.168.2.10
```

### Hop 2

```text
Router0 → Router1

Source MAC      = Router0
Destination MAC = Router1

Source IP       = 192.168.1.10
Destination IP  = 192.168.2.10
```

### Hop 3

```text
Router1 → PC1

Source MAC      = Router1
Destination MAC = PC1

Source IP       = 192.168.1.10
Destination IP  = 192.168.2.10
```

---

# 20. MAC Is Hop-to-Hop; IP Is End-to-End

```text
PC0             Router0             Router1             PC1
 │                 │                   │                  │
 ├──── MAC ───────►│                   │                  │
 │                 ├──── MAC ─────────►│                  │
 │                 │                   ├──── MAC ─────────►│
 │                                                        │
 └─────────────────────── IP ─────────────────────────────►│
```

Remember:

```text
MAC = Hop-to-Hop

IP  = End-to-End
```

---

# 📌 Quick Revision

| Scenario | Device ARPs For |
|---|---|
| PC0 → PC2 | PC2's IP `192.168.1.20` |
| PC0 → PC1 | Default gateway `192.168.1.1` |
| Router0 → Router1 | Next hop `10.0.0.2` |
| Router1 → PC1 | PC1 `192.168.2.10` |

---

# 📌 Important Commands

| Device | Command | Purpose |
|---|---|---|
| Packet Tracer PC | `arp -a` | Display ARP cache |
| Packet Tracer PC | `ping IP` | Generate network traffic |
| Cisco Router | `show ip arp` | Display ARP table |
| Cisco Router | `show ip route` | Display routing table |
| Cisco Router | `show ip interface brief` | Verify interface addressing/status |

---

# 🔑 Key Takeaways

- Build and cable the topology before configuring IP addresses.
- This lab contains **three networks**: `192.168.1.0/24`, `10.0.0.0/24`, and `192.168.2.0/24`.
- PC0 and PC2 are on the same subnet.
- PC1 is on a different subnet from PC0.
- Same-subnet communication causes the sender to ARP for the **destination host**.
- Different-subnet communication causes the sender to ARP for the **default gateway**.
- ARP broadcasts do not cross routers.
- Each Ethernet segment performs its own ARP resolution.
- Routers rebuild the Layer 2 frame at each routed Ethernet hop.
- MAC addresses therefore change from hop to hop.
- IP addresses identify the end hosts during normal routing.

---

# ⭐ Golden Rule

```text
SAME SUBNET
──────────────
ARP for the DESTINATION


DIFFERENT SUBNET
──────────────────
ARP for the DEFAULT GATEWAY
```

Across this lab:

```text
PC0
 │
 │ ARP for Gateway
 ▼
Router0
 │
 │ ARP for Next Hop
 ▼
Router1
 │
 │ ARP for Destination
 ▼
PC1
```

### Student Memory Rule

```text
MAC = Hop-to-Hop

IP  = End-to-End

ARP = Find the MAC required for the next local Ethernet delivery
```