VLSM and Static Routing Lab

Task Overview

In this lab, I had to subnet the 192.168.5.0/24 network using VLSM and provide enough addresses for four different LANs and the point-to-point connection between R1 and R2.

The requirements were also to:

Assign the first usable IP address to the PC in each LAN.

Assign the last usable IP address to the router interface in each LAN.

Configure the correct subnet mask and default gateway on every PC.

Configure the router interfaces.

Configure static routes so all PCs could communicate with each other.

Verify the configuration using ping.

1. Planning the VLSM Subnets

When working with VLSM, I started with the network that required the largest number of hosts and continued from largest to smallest.

This is a useful way to organize the address space efficiently and avoid using address ranges that may later be needed for a larger subnet.

The required networks were:

LAN2 — 64 hosts

LAN1 — 45 hosts

LAN3 — 14 hosts

LAN4 — 9 hosts

R1 ↔ R2 — 2 addresses

2. LAN2 – 64 Hosts

The largest LAN needed 64 host addresses.

A /26 subnet only provides 62 usable addresses, so it was not enough. I therefore used a /25 subnet:

Network: 192.168.5.0/25
Subnet Mask: 255.255.255.128

First Usable: 192.168.5.1
Last Usable: 192.168.5.126
Broadcast: 192.168.5.127

According to the task requirements, I assigned the first usable address to PC2:

IP Address: 192.168.5.1
Subnet Mask: 255.255.255.128
Default Gateway: 192.168.5.126

I assigned the last usable address to R1 GigabitEthernet0/1:

**interface GigabitEthernet0/1**
**ip address 192.168.5.126 255.255.255.128**
**no shutdown**

3. LAN1 – 45 Hosts

The next LAN required 45 hosts, so a /26 subnet was enough because it provides 62 usable addresses.

Network: 192.168.5.128/26
Subnet Mask: 255.255.255.192

First Usable: 192.168.5.129
Last Usable: 192.168.5.190
Broadcast: 192.168.5.191

PC1 configuration:

IP Address: 192.168.5.129
Subnet Mask: 255.255.255.192
Default Gateway: 192.168.5.190

R1 interface configuration:

**interface GigabitEthernet0/0**
**ip address 192.168.5.190 255.255.255.192**
**no shutdown**

4. LAN3 – 14 Hosts

LAN3 needed 14 hosts.

A /28 subnet provides exactly 14 usable host addresses:

Network: 192.168.5.192/28
Subnet Mask: 255.255.255.240

First Usable: 192.168.5.193
Last Usable: 192.168.5.206
Broadcast: 192.168.5.207

PC3 configuration:

IP Address: 192.168.5.193
Subnet Mask: 255.255.255.240
Default Gateway: 192.168.5.206

R2 interface configuration:

**interface GigabitEthernet0/0**
**ip address 192.168.5.206 255.255.255.240**
**no shutdown**

5. LAN4 – 9 Hosts

LAN4 needed 9 hosts.

A /29 would only provide 6 usable addresses, so I used another /28 subnet:

Network: 192.168.5.208/28
Subnet Mask: 255.255.255.240

First Usable: 192.168.5.209
Last Usable: 192.168.5.222
Broadcast: 192.168.5.223

PC4 configuration:

IP Address: 192.168.5.209
Subnet Mask: 255.255.255.240
Default Gateway: 192.168.5.222

R2 interface configuration:

**interface GigabitEthernet0/1**
**ip address 192.168.5.222 255.255.255.240**
**no shutdown**

6. Point-to-Point Network Between R1 and R2

The final subnet was used only for the connection between the two routers.

Since only two IP addresses were required, I used a /30:

Network: 192.168.5.224/30
Subnet Mask: 255.255.255.252

R1: 192.168.5.225
R2: 192.168.5.226
Broadcast: 192.168.5.227

R1:

**interface GigabitEthernet0/0/0**
**ip address 192.168.5.225 255.255.255.252**
**no shutdown**

R2:

**interface GigabitEthernet0/0/0**
**ip address 192.168.5.226 255.255.255.252**
**no shutdown**

A /31 can also be used on supported point-to-point links because both addresses can be used by the two endpoints. In this lab I used /30, which also works correctly and there was enough available address space.

7. Configuring Static Routes

After configuring all the interfaces, each router only knew about its directly connected networks.

To allow communication between all four LANs, I configured static routes for the remote networks.

R1

R1 needed routes to the two LANs behind R2:

**ip route 192.168.5.192 255.255.255.240 192.168.5.226**
**ip route 192.168.5.208 255.255.255.240 192.168.5.226**

This tells R1 that traffic for LAN3 and LAN4 should be sent to R2.

R2

R2 needed routes to the two LANs behind R1:

**ip route 192.168.5.0 255.255.255.128 192.168.5.225**
**ip route 192.168.5.128 255.255.255.192 192.168.5.225**

This tells R2 that traffic for LAN1 and LAN2 should be sent to R1.

8. Testing the Network

After configuring all router interfaces, I made sure that every active interface had been enabled with:

**no shutdown**

I also checked that every PC had the correct:

IP address

Subnet mask

Default gateway

Finally, I used ping between PCs located in different LANs.

ping <destination-ip>

The communication worked successfully, confirming that both the VLSM addressing and the static routing configuration were correct.

______________

What I Learned:

This lab helped me understand VLSM much better and showed me why it is useful when different LANs require different numbers of host addresses.

I practiced choosing the smallest subnet that can still support the required number of hosts, and I learned why it is useful to allocate the largest networks first.

I also became more comfortable identifying the network address, first usable address, last usable address, and broadcast address for each subnet.

Another important part was connecting subnetting with routing. After dividing one /24 network into several smaller subnets, the routers still needed static routes to reach networks that were not directly connected to them.

Finally, I practiced using /30 for a point-to-point router connection and saw how VLSM allows the same original address space to be divided efficiently for very different network sizes.