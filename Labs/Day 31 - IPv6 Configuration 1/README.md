# IPv4/IPv6 Dual-Stack Lab

## Task Overview

In this lab, I kept the existing IPv4 configuration and added IPv6 to create a **dual-stack network**, where the devices could communicate using both IPv4 and IPv6.

The main focus was:

- Enabling IPv6 routing on R1
- Configuring IPv6 addresses on the router interfaces
- Configuring IPv6 on the PCs
- Using the correct IPv6 default gateways
- Testing both IPv4 and IPv6 connectivity

---

## 1. Enabling IPv6 Routing

On R1, I enabled IPv6 routing globally:

**ipv6 unicast-routing**

This allows R1 to route IPv6 traffic between its interfaces.

---

## 2. Configuring IPv6 on R1

Each LAN used its own `/64` IPv6 network:

- LAN1 → `2001:DB8:0:1::/64`
- LAN2 → `2001:DB8:0:2::/64`
- LAN3 → `2001:DB8:0:3::/64`

I configured R1 with the `::1` address in each subnet.

Example:

**interface GigabitEthernet0/0**  
**ipv6 address 2001:DB8:0:1::1/64**  
**no shutdown**

I followed the same process for the other interfaces:

- `2001:DB8:0:2::1/64`
- `2001:DB8:0:3::1/64`

---

## 3. Verifying IPv6 Addresses

I checked the IPv6 configuration using:

**show ipv6 interface brief**

Each active router interface had:

- A manually configured **Global Unicast Address**
- An automatically generated **Link-Local Address**

The Link-Local addresses are used for local IPv6 communication and can also be used as next-hop/default gateway addresses.

---

## 4. Configuring IPv6 on the PCs

I configured each PC with the `::2` address in its subnet.

### PC1

`2001:DB8:0:1::2/64`

### PC2

`2001:DB8:0:2::2/64`

### PC3

`2001:DB8:0:3::2/64`

For each PC, I configured the corresponding R1 interface as the IPv6 default gateway.

---

## 5. Testing Dual-Stack Connectivity

Finally, I tested communication between the PCs using both:

**IPv4 ping**

and:

**IPv6 ping**

For example:

**ping 2001:DB8:0:2::2**

All PCs were able to communicate successfully using both protocols.

---

## What I Learned

This lab helped me understand how **IPv4 and IPv6 can operate at the same time on the same network** using dual stack.

I practiced enabling IPv6 routing, configuring `/64` Global Unicast addresses, identifying automatically generated Link-Local addresses, and configuring IPv6 default gateways.

The key idea is that IPv4 and IPv6 operate independently, but both can coexist on the same interfaces and devices.