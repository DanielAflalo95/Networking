# Dynamic NAT and PAT Lab

## Objective

The goal of this lab was to configure and compare **Dynamic NAT** and **PAT (Port Address Translation)** on a Cisco router.

The lab demonstrated the limitation of a small Dynamic NAT pool and how PAT allows multiple internal hosts to share a single public IP address.

## Network Overview

- Internal LAN: **172.16.0.0/24**
- R1 G0/1: **172.16.0.254/24**
- R1 G0/0: **203.0.113.1/30**
- External Router: **203.0.113.2/30**
- DNS Server: **8.8.8.8**

### Internal Hosts

- PC1: **172.16.0.1**
- PC2: **172.16.0.2**
- PC3: **172.16.0.3**

---

## 1. Configuring NAT Inside and Outside Interfaces

The LAN-facing interface was configured as the **NAT inside interface**:

`interface g0/1`

`ip nat inside`

The Internet-facing interface was configured as the **NAT outside interface**:

`interface g0/0`

`ip nat outside`

---

## 2. Identifying the Internal Network with an ACL

A standard ACL was created to match traffic originating from the internal network:

`access-list 1 permit 172.16.0.0 0.0.0.255`

This ACL identifies the **172.16.0.0/24** network as traffic that should be translated by NAT.

---

## 3. Creating the Dynamic NAT Pool

A Dynamic NAT pool containing only two public addresses was created:

`ip nat pool NAT_POOL 100.0.0.1 100.0.0.2 netmask 255.255.255.0`

The ACL was then connected to the NAT pool:

`ip nat inside source list 1 pool NAT_POOL`

This allowed internal devices to dynamically receive one of the available addresses from the pool.

---

## 4. Testing Dynamic NAT

PC1 and PC2 generated traffic toward the external network.

The router dynamically assigned:

- PC1 → one address from the NAT pool
- PC2 → the second address from the NAT pool

When PC3 attempted to communicate externally, the ping failed.

The reason was that the NAT pool contained only **two available addresses**, while three internal hosts required translation.

This demonstrated an important limitation of Dynamic NAT:

**Each simultaneously translated internal host requires an available address from the NAT pool.**

---

## 5. Removing the Dynamic NAT Configuration

Before changing the NAT method, the existing translations were cleared:

`clear ip nat translation *`

The Dynamic NAT rule was removed:

`no ip nat inside source list 1 pool NAT_POOL`

The NAT pool was then removed:

`no ip nat pool NAT_POOL 100.0.0.1 100.0.0.2 netmask 255.255.255.0`

The pool could not be deleted while it was still being referenced by the NAT configuration, so the NAT rule had to be removed first.

---

## 6. Configuring PAT

PAT was configured using R1's external interface address:

`ip nat inside source list 1 interface g0/0 overload`

The keyword **overload** allows multiple internal hosts to share the same public IP address.

Instead of assigning a different public IP to each host, all internal devices could now use:

**203.0.113.1**

The router distinguishes between different connections using transport-layer port numbers or protocol identifiers.

---

## 7. Testing PAT

After configuring PAT, PC1, PC2, and PC3 were all able to communicate with the external network.

Unlike Dynamic NAT, PC3 no longer required another public IP address.

All three hosts shared the same Inside Global address:

**203.0.113.1**

---

## 8. Examining the NAT Translation Table

The NAT translation table was examined using:

`show ip nat translations`

The table showed that multiple private addresses were translated to the same public address.

For example:

- `172.16.0.1` → `203.0.113.1`
- `172.16.0.2` → `203.0.113.1`
- `172.16.0.3` → `203.0.113.1`

The router used different port numbers or ICMP identifiers to keep the connections separate.

One of the most important observations in this lab was seeing PAT handle a potential identifier collision.

If two internal connections could conflict while using the same public IP, the router can translate the identifier to a different value so that every translation remains unique.

This is what allows many internal devices to safely share one public IP address.

---

## 9. Observing DNS Traffic

The NAT table also showed UDP traffic toward:

**8.8.8.8:53**

Port **53** is used by DNS.

Before a PC could communicate with `google.com`, it first needed to resolve the hostname into an IP address.

The process was:

PC → DNS query to `8.8.8.8:53`

DNS Server → returns the IP address of `google.com`

PC → sends ICMP traffic to the resolved Google IP address

PAT translated both the DNS traffic and the ICMP traffic as they passed through R1.

---

## Key Takeaways

- **Dynamic NAT** assigns public addresses dynamically from a configured pool.
- A Dynamic NAT pool has a limited number of simultaneous translations.
- If the pool is exhausted, additional hosts cannot create new translations.
- **PAT** allows multiple internal hosts to share a single public IP address.
- The `overload` keyword enables PAT.
- PAT distinguishes connections using **port numbers or protocol identifiers**.
- PAT can modify identifiers when necessary to prevent translation conflicts.
- `show ip nat translations` is extremely useful for understanding how NAT and PAT operate in real time.
- DNS traffic can be identified by UDP destination port **53**.
- The lab clearly demonstrated why PAT is much more scalable than basic Dynamic NAT.