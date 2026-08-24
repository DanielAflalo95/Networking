# VLAN Trunking and Router-on-a-Stick Lab

## Task Overview

In this lab, I configured multiple VLANs across two switches and used **Router-on-a-Stick** to allow communication between them.

The networks were:

- **VLAN 10** — `10.0.0.0/26`
- **VLAN 20** — `10.0.0.64/26`
- **VLAN 30** — `10.0.0.128/26`

The main focus was configuring **access ports, trunk links, allowed VLANs, a native VLAN, and router subinterfaces**.

---

## 1. PC Configuration

I configured each PC with its assigned IP address, `/26` subnet mask (`255.255.255.192`), and the correct default gateway.

Default gateways:

- VLAN 10 → `10.0.0.62`
- VLAN 20 → `10.0.0.126`
- VLAN 30 → `10.0.0.190`

---

## 2. Creating the VLANs

I created only the VLANs that were required on each switch.

### SW1

SW1 only has devices belonging to VLAN 10 and VLAN 30:

**vlan 10**  
**vlan 30**

### SW2

SW2 needs all three VLANs:

**vlan 10**  
**vlan 20**  
**vlan 30**

Even though SW2 has no local PC in VLAN 30, VLAN 30 still needs to exist there because its traffic passes through SW2 on the way between SW1 and R1.

I also created an unused VLAN to use as the native VLAN on the trunk between the switches, for example:

**vlan 99**

---

## 3. Configuring Access Ports

I configured the PC-facing interfaces as access ports and assigned them to their correct VLANs.

Example:

**interface FastEthernet0/1**  
**switchport mode access**  
**switchport access vlan 10**

### SW1

- F0/1, F0/2 → VLAN 10
- F0/3, F0/4 → VLAN 30

### SW2

- F0/1 → VLAN 20
- F0/2, F0/3 → VLAN 10

---

## 4. Trunk Between SW1 and SW2

The connection between SW1 and SW2 carries only the VLANs that actually need to cross this link.

In this case, those are **VLAN 10 and VLAN 30**.

On both sides of the trunk:

**interface GigabitEthernet0/1**  
**switchport mode trunk**  
**switchport trunk allowed vlan 10,30**  
**switchport trunk native vlan 99**

VLAN 20 does **not** need to cross this link because it exists only on the SW2 side.

The same native VLAN must be configured on both ends of the trunk.

---

## 5. Trunk Between SW2 and R1

The link between SW2 and R1 needs to carry **all three VLANs**, because R1 performs the routing between them.

On SW2:

**interface GigabitEthernet0/2**  
**switchport mode trunk**  
**switchport trunk allowed vlan 10,20,30**

This is different from the SW1–SW2 trunk because the router must receive traffic from VLAN 10, VLAN 20, and VLAN 30.

---

## 6. Router-on-a-Stick

R1 uses one physical interface with multiple subinterfaces.

First, I enabled the physical interface:

**interface GigabitEthernet0/0**  
**no shutdown**

### VLAN 10

**interface GigabitEthernet0/0.10**  
**encapsulation dot1Q 10**  
**ip address 10.0.0.62 255.255.255.192**

### VLAN 20

**interface GigabitEthernet0/0.20**  
**encapsulation dot1Q 20**  
**ip address 10.0.0.126 255.255.255.192**

### VLAN 30

**interface GigabitEthernet0/0.30**  
**encapsulation dot1Q 30**  
**ip address 10.0.0.190 255.255.255.192**

Each subinterface represents one VLAN and acts as its default gateway.

The **encapsulation dot1Q** command associates each subinterface with the correct VLAN ID.

---

## 7. Testing Connectivity

Finally, I used **ping** between PCs in the same VLAN and between different VLANs.

All PCs were able to communicate successfully, confirming that the access ports, trunks, allowed VLANs, and Router-on-a-Stick configuration were correct.

---

## What I Learned

This lab helped me understand that a trunk does not necessarily need to carry every VLAN on the switch. Using **switchport trunk allowed vlan** allows me to control exactly which VLANs can cross each trunk.

I also learned that a VLAN may need to exist on a switch even when no local PC belongs to it, if that VLAN's traffic needs to pass through the switch.

The main concept was **Router-on-a-Stick**: using one physical router interface with several 802.1Q subinterfaces, where each subinterface acts as the gateway for a different VLAN.