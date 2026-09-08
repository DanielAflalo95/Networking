# IPv6 EUI-64 and Static Routing Lab

## Task Overview

In this lab, I configured IPv6 between two LANs while keeping the existing IPv4 configuration, creating a **dual-stack network**.

The main focus was:

- Configuring IPv6 addresses using **EUI-64**
- Enabling IPv6 without manually assigning a Global Unicast address
- Working with **Link-Local addresses**
- Configuring IPv6 static routes using Link-Local next hops

---

## 1. Enabling IPv6 Routing

First, I enabled IPv6 routing globally on both R1 and R2:

**ipv6 unicast-routing**

This allows the routers to forward IPv6 traffic between networks.

---

## 2. Configuring IPv6 with EUI-64

### R1 G0/1

R1's G0/1 MAC address:

`0030.f236.4502`

Using Modified EUI-64:

`0030.f236.4502`  
→ `00:30:F2:FF:FE:36:45:02`  
→ Flip the U/L bit: `00 → 02`

Interface ID:

`230:F2FF:FE36:4502`

I configured:

**interface GigabitEthernet0/1**  
**ipv6 address 2001:DB8::/64 eui-64**

This generated:

`2001:DB8::230:F2FF:FE36:4502/64`

---

### R2 G0/1

R2's G0/1 MAC address:

`0001.63b0.b802`

The generated EUI-64 Interface ID is:

`201:63FF:FEB0:B802`

Configuration:

**interface GigabitEthernet0/1**  
**ipv6 address 2001:DB8:0:1::/64 eui-64**

Generated address:

`2001:DB8:0:1:201:63FF:FEB0:B802/64`

---

## 3. Configuring the PCs

### PC1

- IPv6 Address → `2001:DB8::2/64`
- Default Gateway → `2001:DB8::230:F2FF:FE36:4502`

### PC2

- IPv6 Address → `2001:DB8:0:1::2/64`
- Default Gateway → `2001:DB8:0:1:201:63FF:FEB0:B802`

---

## 4. Enabling IPv6 on the R1–R2 Link

The task required enabling IPv6 on G0/0 without manually configuring a Global Unicast address.

On both routers:

**interface GigabitEthernet0/0**  
**ipv6 enable**

This automatically created a Link-Local address on each interface.

The resulting addresses were:

- R1 G0/0 → `FE80::230:F2FF:FE36:4501`
- R2 G0/0 → `FE80::201:63FF:FEB0:B801`

I verified them using:

**show ipv6 interface brief**

---

## 5. Configuring IPv6 Static Routes

R1 needed a route toward PC2's network:

`2001:DB8:0:1::/64`

Using R2's Link-Local address as the next hop:

**ipv6 route 2001:DB8:0:1::/64 GigabitEthernet0/0 FE80::201:63FF:FEB0:B801**

R2 needed a route toward PC1's network:

`2001:DB8::/64`

Using R1's Link-Local address:

**ipv6 route 2001:DB8::/64 GigabitEthernet0/0 FE80::230:F2FF:FE36:4501**

When using a **Link-Local address as the next hop**, I also had to specify the outgoing interface because Link-Local addresses are only meaningful on their local link.

---

## 6. Verifying the Configuration

I checked the IPv6 routing tables using:

**show ipv6 route**

Then I tested communication between PC1 and PC2 using IPv6 `ping`.

For example, from PC1:

**ping 2001:DB8:0:1::2**

The ping was successful, confirming that IPv6 routing was working correctly.

---

## What I Learned

This lab helped me practice using **Modified EUI-64** to automatically generate the Interface ID portion of an IPv6 address from a MAC address.

I also learned that **ipv6 enable** can activate IPv6 on an interface and automatically create a Link-Local address without requiring a Global Unicast address.

The most important new concept was using **Link-Local addresses as next hops in IPv6 static routes**. Because Link-Local addresses only exist within a local link, the outgoing interface must also be specified in the route.

Finally, the lab reinforced how IPv4 and IPv6 can operate together in the same network using **dual stack**.