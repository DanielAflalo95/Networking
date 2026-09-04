# OSPF Default Route & ASBR Lab

## Task Overview

In this lab, I configured **OSPF Area 0** between four routers, added loopback interfaces, configured passive interfaces where needed, and configured **R1 as an ASBR** to advertise a default route toward the rest of the OSPF network.

The main focus was understanding how an OSPF router can distribute a **default route** learned from outside the OSPF domain.

---

## 1. Basic Configuration

I configured the required hostnames, IP addresses, subnet masks, default gateway on PC1, and enabled all active router interfaces.

Main networks:

- R1 ↔ R2 → `10.0.12.0/30`
- R1 ↔ R3 → `10.0.13.0/30`
- R2 ↔ R4 → `10.0.24.0/30`
- R3 ↔ R4 → `10.0.34.0/30`
- R4 LAN → `192.168.4.0/24`
- R1 ↔ ISP → `203.0.113.0/30`

The Internet-facing interface on R1 was **not included in OSPF**.

---

## 2. Configuring Loopback Interfaces

I configured one `/32` loopback on each router:

- R1 → `1.1.1.1/32`
- R2 → `2.2.2.2/32`
- R3 → `3.3.3.3/32`
- R4 → `4.4.4.4/32`

Example:

**interface Loopback0**  
**ip address 1.1.1.1 255.255.255.255**

These loopbacks were also advertised through OSPF.

---

## 3. Configuring OSPF

I enabled OSPF on all internal router links and placed them in **Area 0**.

Example on R1:

**router ospf 1**  
**network 10.0.12.0 0.0.0.3 area 0**  
**network 10.0.13.0 0.0.0.3 area 0**  
**network 1.1.1.1 0.0.0.0 area 0**

I followed the same process on R2, R3, and R4 using their connected networks.

On R4, I also advertised:

**network 192.168.4.0 0.0.0.255 area 0**

---

## 4. Configuring Passive Interfaces

Interfaces that needed to be advertised but did not need to form OSPF neighbor relationships were configured as passive.

On every router:

**passive-interface Loopback0**

On R4, the interface toward the PC LAN was also passive:

**passive-interface GigabitEthernet0/0**

This allows the network to remain advertised in OSPF without sending OSPF Hello packets toward end devices.

---

## 5. Configuring R1 as an ASBR

R1 connects the OSPF network to the external ISP network, so it acts as an **ASBR — Autonomous System Boundary Router**.

First, I configured a default static route toward the ISP:

**ip route 0.0.0.0 0.0.0.0 203.0.113.2**

This tells R1:

> For any destination that is not already known, send the traffic toward ISP1.

Then, under OSPF, I advertised that default route into the OSPF domain:

**router ospf 1**  
**default-information originate**

This allows R2, R3, and R4 to learn that R1 is the way out toward external networks.

---

## 6. Checking the Default Route

I checked the routing tables using:

**show ip route**

On the other OSPF routers, the default route appeared as:

**O\*E2 0.0.0.0/0**

For example, R4 could learn two equal paths toward the ASBR:

**O\*E2 0.0.0.0/0 via 10.0.24.1**  
**O\*E2 0.0.0.0/0 via 10.0.34.1**

`O` means the route was learned through OSPF, `*` marks it as a candidate default route, and `E2` means it is an **OSPF External Type 2 route**.

If both paths have the same effective OSPF cost, OSPF can install both and use **Equal-Cost Multi-Path (ECMP)**.

---

## What I Learned

This lab helped me understand the role of an **ASBR** in OSPF and how a router can introduce a default route from outside the OSPF domain.

I learned the difference between creating a local static default route with **ip route 0.0.0.0 0.0.0.0** and actually advertising that route to other OSPF routers using **default-information originate**.

I also practiced identifying **O\*E2 routes**, using passive interfaces correctly, and seeing how OSPF can install multiple equal-cost routes toward the same destination.