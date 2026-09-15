# Static NAT Lab

## Objective

The goal of this lab was to configure and verify **Static NAT** on a Cisco router so that devices in a private network could communicate with an external server.

## Network Overview

- Internal LAN: **172.16.0.0/24**
- R1 G0/1: **172.16.0.254/24**
- R1 G0/0: **203.0.113.1/30**
- External Router: **203.0.113.2/30**
- External Server: **8.8.8.8**

### Internal Hosts

- PC1: **172.16.0.1**
- PC2: **172.16.0.2**
- PC3: **172.16.0.3**

### Static NAT Mappings

- **172.16.0.1 → 100.0.0.1**
- **172.16.0.2 → 100.0.0.2**
- **172.16.0.3 → 100.0.0.3**

## 1. Testing Connectivity Before NAT

I first tested connectivity from the internal network to the external server using:

`ping 8.8.8.8`

The ping failed because the internal host was using a **private IP address**, and no NAT configuration existed yet to translate it to an address that could be used on the external network.

## 2. Configuring NAT Inside and Outside Interfaces

The LAN-facing interface was configured as the **NAT inside interface**:

`interface g0/1`

`ip nat inside`

The external-facing interface was configured as the **NAT outside interface**:

`interface g0/0`

`ip nat outside`

## 3. Configuring Static NAT

I configured a permanent one-to-one mapping for each internal host:

`ip nat inside source static 172.16.0.1 100.0.0.1`

`ip nat inside source static 172.16.0.2 100.0.0.2`

`ip nat inside source static 172.16.0.3 100.0.0.3`

Each **Inside Local** address was mapped to a fixed **Inside Global** address.

## 4. Verifying the NAT Configuration

I verified the configured NAT mappings using:

`show ip nat translations`

The router displayed the permanent Static NAT mappings:

- **100.0.0.1 → 172.16.0.1**
- **100.0.0.2 → 172.16.0.2**
- **100.0.0.3 → 172.16.0.3**

## 5. Testing Connectivity After NAT

After configuring Static NAT, I tested connectivity again:

`ping 8.8.8.8`

The communication succeeded.

I then checked the NAT table again:

`show ip nat translations`

The output showed temporary **ICMP translation entries** in addition to the permanent Static NAT mappings.

This confirmed that the router was translating the internal source addresses to their configured **Inside Global addresses**.

## 6. Clearing NAT Translations

I cleared the active translation entries using:

`clear ip nat translation *`

Then I verified the NAT table again:

`show ip nat translations`

The temporary ICMP translation entries were removed, while the permanent Static NAT mappings remained.

## Key Takeaways

- `ip nat inside` defines the interface connected to the internal network.
- `ip nat outside` defines the interface connected to the external network.
- **Static NAT** creates a permanent one-to-one mapping between an Inside Local address and an Inside Global address.
- `show ip nat translations` displays configured NAT mappings and active translations.
- `clear ip nat translation *` removes temporary translation entries while the configured Static NAT mappings remain.