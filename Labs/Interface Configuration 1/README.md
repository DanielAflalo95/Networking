Basic Cisco Network Configuration Lab

Task Overview

In this lab, I configured a small network with one router, two switches, and four PCs.

The main requirements were to:

Configure hostnames for R1, SW1, and SW2

Configure the correct IP addresses

Manually configure speed and duplex on links between networking devices

Add descriptions to the interfaces

Disable unused switch interfaces

Configure the PCs with the correct IP address, subnet mask, and default gateway

Test connectivity between the devices

The network used the 172.16.0.0/16 network, with the subnet mask 255.255.0.0.

Router Configuration

I started with R1 by entering Privileged EXEC Mode and then Global Configuration Mode:

**enable**
**configure terminal**

I changed the router hostname:

**hostname R1**

Then I entered the interface connected to SW1:

**interface GigabitEthernet0/0**

I configured the router IP address:

**ip address 172.16.255.254 255.255.0.0**

Since the network uses /16, the subnet mask is 255.255.0.0.

Because this interface connects the router to another networking device, I manually configured its speed and duplex:

**speed 1000**
**duplex full**

I also added a description:

**description To SW1**

Finally, I enabled the interface:

**no shutdown**

This was important because router interfaces are administratively disabled by default. Until I used **no shutdown**, the router could not establish a connection with SW1.

Switch Configuration

On both switches, I followed the same basic process:

**enable**
**configure terminal**
**hostname SW1**

And on the second switch:

**hostname SW2**

For interfaces connecting networking devices, such as SW1 to R1, SW1 to SW2, and SW2 to SW1, I manually configured the speed and duplex.

For example:

**interface GigabitEthernet0/1**
**speed 1000**
**duplex full**
**description To R1**

On the connection between SW1 and SW2, I also added the correct interface descriptions:

**description To SW2**

or:

**description To SW1**

For interfaces connected to PCs, I added descriptions such as:

**description To PC1**
**description To PC2**
**description To PC3**
**description To PC4**

Disabling Unused Interfaces

Another important part of the lab was shutting down interfaces that were not being used.

On SW1, FastEthernet0/3 through FastEthernet0/24 were unused, so I used:

**interface range FastEthernet0/3 - 24**
**shutdown**

On SW2, GigabitEthernet0/2 was also unused, so I used:

**interface range FastEthernet0/3 - 24, GigabitEthernet0/2**
**shutdown**

Using **interface range** made it possible to configure multiple interfaces at the same time instead of entering each one separately.

PC Configuration

I configured each PC with its own IP address:

PC1 — 172.16.0.1

PC2 — 172.16.0.2

PC3 — 172.16.0.3

PC4 — 172.16.0.4

All PCs used:

Subnet Mask: 255.255.0.0
Default Gateway: 172.16.255.254

The default gateway is the IP address of the router interface, which allows the PCs to send traffic to networks outside their local network when needed.

Testing Connectivity

After finishing the configuration, I used the **ping** command between different PCs to make sure the network was working correctly.

For example:

**ping 172.16.0.4**

The ping tests were successful, which confirmed that the devices were configured correctly and communication between the PCs was working.

What I Learned

In this lab, I practiced the basic Cisco IOS configuration process and became more comfortable moving between configuration modes.

I also learned how to configure IP addresses, subnet masks, speed and duplex settings, interface descriptions, and default gateways.

Another useful part of the exercise was learning how to use **interface range** to manage multiple switch ports and why unused interfaces should be disabled.

Finally, using **ping** helped me understand how to verify that the network configuration is actually working after completing the setup.