# VLAN Configuration Lab

## Task Overview

In this lab, I configured three different VLANs on the same switch:

- **VLAN 10 – Engineering**
- **VLAN 20 – HR**
- **VLAN 30 – Sales**

Each VLAN uses a `/26` subnet, which means the subnet mask is `255.255.255.192`.

The goal was to configure the PCs, assign the correct switch ports to each VLAN, connect each VLAN to a separate router interface, and verify communication between all PCs.

---

## 1. Configuring the PCs

I started by configuring the IP address, subnet mask, and default gateway on each PC.

The instructions required using the **last usable IP address** in each subnet as the default gateway.

### VLAN 10 – Engineering

Network: `10.0.0.0/26`

- First usable: `10.0.0.1`
- Last usable: `10.0.0.62`
- Broadcast: `10.0.0.63`

PC1:

- IP Address: `10.0.0.1`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.62`

PC2:

- IP Address: `10.0.0.2`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.62`

### VLAN 20 – HR

Network: `10.0.0.64/26`

- First usable: `10.0.0.65`
- Last usable: `10.0.0.126`
- Broadcast: `10.0.0.127`

PC3:

- IP Address: `10.0.0.65`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.126`

PC4:

- IP Address: `10.0.0.66`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.126`

### VLAN 30 – Sales

Network: `10.0.0.128/26`

- First usable: `10.0.0.129`
- Last usable: `10.0.0.190`
- Broadcast: `10.0.0.191`

PC5:

- IP Address: `10.0.0.129`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.190`

PC6:

- IP Address: `10.0.0.130`
- Subnet Mask: `255.255.255.192`
- Default Gateway: `10.0.0.190`

---

## 2. Configuring the Router

Next, I moved to the CLI of R1.

I entered Privileged EXEC Mode and Global Configuration Mode:

**enable**  
**configure terminal**

Each VLAN has its own physical interface on the router.

### VLAN 10 Interface

**interface GigabitEthernet0/0**  
**ip address 10.0.0.62 255.255.255.192**  
**no shutdown**

### VLAN 20 Interface

**interface GigabitEthernet0/1**  
**ip address 10.0.0.126 255.255.255.192**  
**no shutdown**

### VLAN 30 Interface

**interface GigabitEthernet0/2**  
**ip address 10.0.0.190 255.255.255.192**  
**no shutdown**

Each router interface uses the same IP address that was configured as the default gateway for the PCs in that VLAN.

Because all three networks are directly connected to R1, I did not need to configure static routes between them. The router already knows about all three directly connected networks.

---

## 3. Creating the VLANs

Next, I configured the VLANs on SW1 and gave each VLAN a descriptive name.

For VLAN 10:

**vlan 10**  
**name ENGINEERING**

For VLAN 20:

**vlan 20**  
**name HR**

For VLAN 30:

**vlan 30**  
**name SALES**

This created three separate Layer 2 broadcast domains on the switch.

---

## 4. Assigning Switch Interfaces to the VLANs

For each VLAN, I configured three switch interfaces together:

- Two interfaces connected to PCs
- One interface connected to the router

I used the **interface range** command so the interfaces belonging to the same VLAN could be configured together.

### VLAN 10 – Engineering

The ports connected to PC1, PC2, and the VLAN 10 router interface were configured as access ports:

**interface range FastEthernet3/1, FastEthernet4/1, GigabitEthernet0/1**  
**switchport mode access**  
**switchport access vlan 10**

### VLAN 20 – HR

The ports connected to PC3, PC4, and the VLAN 20 router interface were assigned to VLAN 20:

**interface range FastEthernet5/1, FastEthernet6/1, GigabitEthernet1/1**  
**switchport mode access**  
**switchport access vlan 20**

### VLAN 30 – Sales

The ports connected to PC5, PC6, and the VLAN 30 router interface were assigned to VLAN 30:

**interface range FastEthernet7/1, FastEthernet8/1, GigabitEthernet2/1**  
**switchport mode access**  
**switchport access vlan 30**

The command **switchport mode access** configures the interface as a Layer 2 access port.

The command **switchport access vlan 10** assigns the interface to VLAN 10. The same process was used for VLAN 20 and VLAN 30.

---

## 5. Testing Connectivity

After completing the configuration, I used the **ping** command between PCs to verify connectivity.

For example, communication between PCs in different VLANs had to pass through the router:

`PC1 → SW1 → R1 → SW1 → PC5`

The router receives traffic from one VLAN, routes it to the correct destination network, and sends it through the interface connected to the destination VLAN.

All of my ping tests worked successfully, confirming that the VLAN configuration, default gateways, router interfaces, and inter-VLAN routing were working correctly.

---

## What I Learned

This lab helped me understand that **VLANs separate devices into different Layer 2 networks**, even when all devices are connected to the same physical switch.

I also learned that devices in different VLANs cannot communicate directly through the switch alone. They need a **Layer 3 device**, in this case R1, to route traffic between the different networks.

I practiced creating and naming VLANs, configuring switch ports as **access ports**, and assigning multiple interfaces to the correct VLAN using **interface range**.

I also understood the relationship between the PC's **default gateway** and the router interface connected to its VLAN. Each VLAN needs its own gateway address from the same subnet.

Finally, this exercise showed me how VLAN configuration and routing work together to allow communication between logically separated networks.