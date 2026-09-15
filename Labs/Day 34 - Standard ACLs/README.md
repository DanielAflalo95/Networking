# OSPF + Standard ACLs Lab

## Objective

Configure OSPF between R1 and R2, then apply:

- Standard **Numbered ACLs** on R1
- Standard **Named ACLs** on R2

The ACLs must enforce the required access restrictions between the different networks.

## 1. Configure OSPF

### R1

```cisco
router ospf 1
 network 172.16.1.0 0.0.0.255 area 0
 network 172.16.2.0 0.0.0.255 area 0
 network 203.0.113.0 0.0.0.3 area 0
```

### R2

```cisco
router ospf 1
 network 192.168.1.0 0.0.0.255 area 0
 network 192.168.2.0 0.0.0.255 area 0
 network 203.0.113.0 0.0.0.3 area 0
```

Verify routing:

```cisco
show ip ospf neighbor
show ip route
```

Before configuring ACLs, I verified that all networks had full connectivity.

## 2. R1 – Standard Numbered ACLs

Block `172.16.1.0/24` from accessing `172.16.2.0/24`:

```cisco
access-list 10 deny 172.16.1.0 0.0.0.255
access-list 10 permit any

interface g0/1
 ip access-group 10 out
```

Block `172.16.2.0/24` from accessing `172.16.1.0/24`:

```cisco
access-list 20 deny 172.16.2.0 0.0.0.255
access-list 20 permit any

interface g0/0
 ip access-group 20 out
```

## 3. R2 – Standard Named ACLs

Allow only PC1 and PC3 to access `192.168.1.0/24`:

```cisco
ip access-list standard ACCESS_192_168_1
 permit host 172.16.1.1
 permit host 172.16.2.1
 deny any

interface g0/0
 ip access-group ACCESS_192_168_1 out
```

Block `172.16.2.0/24` from accessing `192.168.2.0/24`:

```cisco
ip access-list standard BLOCK_172_16_2
 deny 172.16.2.0 0.0.0.255
 permit any

interface g0/1
 ip access-group BLOCK_172_16_2 out
```

## 4. Verification

I tested the required connections using `ping` and checked the ACLs with:

```cisco
show access-lists
show ip interface
```

The allowed traffic succeeded and the restricted traffic was blocked.

## What I Learned

- How to configure OSPF between multiple networks.
- The difference between **Standard Numbered ACLs** and **Standard Named ACLs**.
- Standard ACLs filter traffic based only on the **source IP address**.
- Standard ACLs should usually be placed close to the **destination**.
- `in` filters traffic entering an interface, while `out` filters traffic leaving it.
- It is useful to verify full routing connectivity before applying ACLs, so routing and filtering problems can be troubleshooted separately.