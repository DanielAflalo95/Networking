# OSPF Interface Configuration, Reference Bandwidth & ASBR Lab

## Task Overview

In this lab, I configured **OSPF Area 0** between four routers, but this time OSPF was enabled **directly on the interfaces** instead of using `network` commands.

The main new topics were:

- Enabling OSPF directly on interfaces
- Changing the **OSPF Reference Bandwidth**
- Observing how the new costs affect route selection
- Inspecting **OSPF Hello packets** in Simulation Mode

---

## 1. Basic Configuration

I configured the required hostnames, IP addresses, subnet masks, loopbacks, and enabled all active router interfaces.

The Internet-facing link between R1 and ISP1 was **not included in OSPF**.

Loopbacks:

- R1 → `1.1.1.1/32`
- R2 → `2.2.2.2/32`
- R3 → `3.3.3.3/32`
- R4 → `4.4.4.4/32`

---

## 2. Enabling OSPF Directly on Interfaces

Instead of using OSPF `network` commands, I enabled OSPF directly on each required interface.

For example:

**interface GigabitEthernet0/0**  
**ip ospf 1 area 0**

I repeated this on all router interfaces participating in OSPF, including the loopback interfaces.

For example:

**interface Loopback0**  
**ip ospf 1 area 0**

This provides a more direct way of controlling exactly which interfaces participate in OSPF.

---

## 3. Configuring Passive Interfaces

Interfaces that needed to be advertised but did not need to form OSPF neighbor relationships were configured as passive.

For example:

**router ospf 1**  
**passive-interface Loopback0**

On R4, I also configured the interface toward the PC LAN as passive.

The network is still advertised, but OSPF Hello packets are not sent through that interface.

---

## 4. Configuring the Reference Bandwidth

The lab required a **FastEthernet interface to have an OSPF cost of 100**.

OSPF calculates cost using:

`Cost = Reference Bandwidth / Interface Bandwidth`

FastEthernet = `100 Mbps`

To get:

`Cost = 100`

the Reference Bandwidth must be:

`10,000 Mbps`

Therefore, I configured the following on **every OSPF router**:

**router ospf 1**  
**auto-cost reference-bandwidth 10000**

With this configuration:

- FastEthernet 100 Mbps → Cost `100`
- GigabitEthernet 1 Gbps → Cost `10`

It is important to configure the same Reference Bandwidth on all OSPF routers so they calculate interface costs consistently.

---

## 5. Configuring R1 as an ASBR

R1 connects the OSPF domain to ISP1, so it acts as the **ASBR**.

First, I configured a default route toward the ISP:

**ip route 0.0.0.0 0.0.0.0 203.0.113.2**

Then I advertised that default route into OSPF:

**router ospf 1**  
**default-information originate**

The other OSPF routers can now use R1 as their gateway toward external networks.

---

## 6. Checking the Default Route on R4

I checked R4's routing table using:

**show ip route**

The default route appears as an OSPF external route:

**O\*E2 0.0.0.0/0**

Because the Reference Bandwidth now distinguishes FastEthernet from GigabitEthernet, the two paths toward R1 no longer have the same internal OSPF cost.

The path:

`R4 → R2 → R1`

has a lower OSPF cost because it includes a GigabitEthernet link between R2 and R1.

Therefore, R4 prefers this path over:

`R4 → R3 → R1`

which uses FastEthernet links along the path.

---

## 7. Inspecting OSPF Hello Messages

Finally, I used **Packet Tracer Simulation Mode** to inspect the OSPF Hello packets exchanged between routers.

The Hello information includes fields such as:

- Network Mask
- Hello Interval
- Dead Interval
- Router Priority
- Designated Router (DR)
- Backup Designated Router (BDR)
- Neighbor Router IDs

The OSPF packet header also contains information such as the **Router ID** and **Area ID**.

These values help OSPF routers determine whether they are compatible and able to form a neighbor relationship.

---

## What I Learned

The main new concept in this lab was enabling OSPF **directly on router interfaces** using **ip ospf 1 area 0**, instead of matching interfaces with `network` commands.

I also learned how the **Reference Bandwidth** affects OSPF cost calculation and why the default value of 100 Mbps does not properly distinguish between FastEthernet and faster modern interfaces.

Finally, I inspected OSPF Hello packets and saw some of the information routers exchange when discovering neighbors and forming OSPF adjacencies.