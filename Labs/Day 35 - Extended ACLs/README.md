# Extended ACL Lab

## Objective

Configure **Extended ACLs** to enforce the following policies:

- Hosts in `172.16.2.0/24` cannot communicate with PC1 (`172.16.1.1`).
- Hosts in `172.16.1.0/24` cannot access the DNS service on SRV1 (`192.168.1.100`).
- Hosts in `172.16.2.0/24` cannot access HTTP or HTTPS services on SRV2 (`192.168.2.100`).

Since Extended ACLs can filter by source, destination, protocol, and port, I placed them **close to the source**.

## 1. Filter Traffic from 172.16.2.0/24

Both restrictions originate from `172.16.2.0/24`, so I combined them into one ACL and applied it inbound on R1 `G0/1`.

```cisco
ip access-list extended FILTER_172_16_2
 deny ip 172.16.2.0 0.0.0.255 host 172.16.1.1
 deny tcp 172.16.2.0 0.0.0.255 host 192.168.2.100 eq 80
 deny tcp 172.16.2.0 0.0.0.255 host 192.168.2.100 eq 443
 permit ip any any

interface g0/1
 ip access-group FILTER_172_16_2 in
```

This blocks:

- All communication from `172.16.2.0/24` to PC1.
- HTTP traffic to SRV2.
- HTTPS traffic to SRV2.

All other traffic is allowed.

## 2. Block DNS Access to SRV1

Hosts in `172.16.1.0/24` must not access DNS on SRV1.

I applied the ACL inbound on R1 `G0/0`.

```cisco
ip access-list extended BLOCK_DNS
 deny udp 172.16.1.0 0.0.0.255 host 192.168.1.100 eq 53
 deny tcp 172.16.1.0 0.0.0.255 host 192.168.1.100 eq 53
 permit ip any any

interface g0/0
 ip access-group BLOCK_DNS in
```

Both TCP and UDP port `53` were blocked to fully restrict DNS access.

## 3. Verification

I verified the ACLs with:

```cisco
show access-lists
show ip interface
```

I then tested the required connections to make sure the specified traffic was blocked while unrelated traffic remained allowed.

## What I Learned

- Extended ACLs can filter by **source IP, destination IP, protocol, and port**.
- Extended ACLs should normally be placed **close to the source**.
- Multiple restrictions from the same source network can be combined into one ACL.
- DNS uses port `53`, HTTP uses `80`, and HTTPS uses `443`.
- `permit ip any any` allows all traffic that does not match the deny rules and prevents the implicit deny from blocking everything else.