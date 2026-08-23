Static Routing Lab – Cisco Packet Tracer

Task Overview

In this lab, I configured a small network with three routers, two switches, and two PCs.

The goal was to:

Configure the hostnames and IP addresses on all routers

Configure the PCs with the correct IP address, subnet mask, and default gateway

Enable all router interfaces

Configure static routes so PC1 and PC2 could communicate through all three routers

Verify the configuration using **show ip route** and **ping**

The switches did not require any configuration in this exercise.

1. Configuring R1

I started on R1 by entering Privileged EXEC Mode and then Global Configuration Mode:

**enable**
**configure terminal**

Then I configured the hostname:

**hostname R1**

Interface toward the PC1 network

R1 is the default gateway for the 192.168.1.0/24 network, so I configured its LAN interface like this:

**interface GigabitEthernet0/1**
**ip address 192.168.1.254 255.255.255.0**
**no shutdown**

Interface toward R2

The connection between R1 and R2 uses the 192.168.12.0/24 network:

**interface GigabitEthernet0/0**
**ip address 192.168.12.1 255.255.255.0**
**no shutdown**

I did not configure speed or duplex manually in this lab because the task did not require it. The interfaces can use their default auto-negotiation settings.

2. Configuring R2

Next, I configured R2:

**enable**
**configure terminal**
**hostname R2**

Interface toward R1

**interface GigabitEthernet0/0**
**ip address 192.168.12.2 255.255.255.0**
**no shutdown**

Interface toward R3

The connection between R2 and R3 uses the 192.168.13.0/24 network:

**interface GigabitEthernet0/1**
**ip address 192.168.13.2 255.255.255.0**
**no shutdown**

3. Configuring R3

Then I configured R3:

**enable**
**configure terminal**
**hostname R3**

Interface toward R2

**interface GigabitEthernet0/0**
**ip address 192.168.13.3 255.255.255.0**
**no shutdown**

Interface toward the PC2 network

R3 is the default gateway for the 192.168.3.0/24 network:

**interface GigabitEthernet0/1**
**ip address 192.168.3.254 255.255.255.0**
**no shutdown**

4. Configuring the PCs

For PC1, I configured:

IP Address: 192.168.1.1
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.1.254

The default gateway is R1 because that is the router connected to PC1's local network.

For PC2, I configured:

IP Address: 192.168.3.1
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.3.254

Here, the default gateway is R3 because that is the router connected to PC2's local network.

5. Configuring Static Routes

At this point, every router only knew the networks connected directly to it.

To make PC1 and PC2 communicate, I had to manually tell each router how to reach the remote networks.

The basic static route command is:

**ip route <destination-network> <subnet-mask> <next-hop-ip>**

Static Route on R1

R1 does not know how to reach the 192.168.3.0/24 network.

The next router in that direction is R2 at 192.168.12.2, so I configured:

**ip route 192.168.3.0 255.255.255.0 192.168.12.2**

In simple terms:

To reach the 192.168.3.0/24 network, send the traffic to R2.

Static Routes on R2

R2 is in the middle of the topology, so it needs a route in both directions.

To reach the PC1 network:

**ip route 192.168.1.0 255.255.255.0 192.168.12.1**

To reach the PC2 network:

**ip route 192.168.3.0 255.255.255.0 192.168.13.3**

So R2 now knows:

192.168.1.0/24 → send toward R1

192.168.3.0/24 → send toward R3

Static Route on R3

R3 also needs to know how to return traffic to PC1's network.

The next hop is R2 at 192.168.13.2:

**ip route 192.168.1.0 255.255.255.0 192.168.13.2**

This gives the network a complete path in both directions:

PC1 → R1 → R2 → R3 → PC2
PC2 → R3 → R2 → R1 → PC1

6. Checking the Routing Table

After configuring the static routes, I checked the routing table with:

**show ip route**

For example, on R2 I could see:

S 192.168.1.0/24 via 192.168.12.1
S 192.168.3.0/24 via 192.168.13.3

The S means Static Route.

I could also see the directly connected networks marked with C, which helped me confirm that R2 knew how to reach both remote LANs.

7. Testing Connectivity

Finally, I tested communication from PC1 to PC2:

**ping 192.168.3.1**

During the first ping, some packets timed out.

That can happen because the devices first need to perform ARP resolution and learn the MAC addresses required on the different Ethernet segments along the path.

After that information was learned, I sent the ping again:

**ping 192.168.3.1**

This time, all four packets were successful:

0% packet loss

That confirmed that the routing configuration was working correctly from PC1 all the way to PC2.

What I Learned

In this lab, I got a clearer understanding of how static routing works between several routers.

I learned that a static route normally points to a destination network, for example:

192.168.3.0/24

rather than only to one specific host such as 192.168.3.1.

I also practiced choosing the correct next-hop IP address and understood why routing has to work in both directions. It is not enough for PC1's traffic to reach PC2 — the routers also need a valid return path back to PC1.

I also practiced using **show ip route** and reading the routing table, especially the difference between connected routes (C) and static routes (S).

Finally, the first ping helped me connect two important concepts: routing decides where the packet should go next, while ARP finds the MAC address needed to actually send the frame on the local Ethernet segment.