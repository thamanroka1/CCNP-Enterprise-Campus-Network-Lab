# CCNP Enterprise Campus Network Lab

**Status: Proposed corrected configuration — verification pending.**

A seven-device Cisco IOSv/IOSvL2 lab based on supplied `show running-config` outputs. Demonstrates VLAN segmentation, multilayer distribution switching, HSRP first-hop redundancy, MST root placement, LACP EtherChannel, DHCP, static routing, and edge NAT/PAT.

## Logical topology

```text
                        DHCP/WAN
                           |
                          R7
                   12.12.12.1  13.13.13.1
                       /          \
             12.12.12.2          13.13.13.2
                    DSW1 ======== DSW2
                      ||  Po1 LACP ||
                 [Campus access layer]
                  ASW1 ASW2 ASW3 ASW4
                  10/20 30/40 50/60 70/80
```

**Important:** Physical access-switch-to-distribution-switch port connections were not provided, so the access-layer drawing is conceptual, not an asserted cabling map. DSW1 Gi1/3 (10.10.10.2) and DSW2 Gi1/2 (11.11.11.2) remain configured but their upstream peers are unknown.

## VLAN addressing

| VLAN | Network | DSW1 SVI | DSW2 SVI | HSRP VIP | Preferred HSRP active |
|---|---|---|---|---|---|
| 10 | 192.168.10.0/24 | 192.168.10.200 | 192.168.10.100 | 192.168.10.254 | DSW1 |
| 20 | 192.168.20.0/24 | 192.168.20.200 | 192.168.20.100 | 192.168.20.254 | DSW1 |
| 30 | 192.168.30.0/24 | 192.168.30.200 | 192.168.30.100 | 192.168.30.254 | DSW1 |
| 40 | 192.168.40.0/24 | 192.168.40.200 | 192.168.40.100 | 192.168.40.254 | DSW1 |
| 50 | 192.168.50.0/24 | 192.168.50.200 | 192.168.50.100 | 192.168.50.254 | DSW2 |
| 60 | 192.168.60.0/24 | 192.168.60.200 | 192.168.60.100 | 192.168.60.254 | DSW2 |
| 70 | 192.168.70.0/24 | 192.168.70.200 | 192.168.70.100 | 192.168.70.254 | DSW2 |
| 80 | 192.168.80.0/24 | 192.168.80.200 | 192.168.80.100 | 192.168.80.254 | DSW2 |

## Design and changes from source

- **MST:** Shared region `nilrupa`, revision 1. Instance 1 includes VLANs 10–40 (DSW1 lower priority); instance 2 includes VLANs 50–80 (DSW2 lower priority).
- **HSRP:** Distinct groups 10–80, matching VLAN IDs; preferred active switch has priority 110 and peer 100, both with preemption. Original configs had DSW2 priority 150 for all VLANs.
- **DHCP:** DSW1 only, with .100, .200, and .254 excluded from each pool. Original had overlapping DHCP pools on both distribution switches. **This removes duplicate DHCP allocation risk but leaves DHCP as a single point of failure.** For production, use a supported failover DHCP service.
- **EtherChannel:** DSW1/DSW2 Gi1/0–Gi1/1 use LACP active in Port-channel1. Confirm actual cabling.
- **Routing:** DSW1 defaults to R7 at 12.12.12.1; DSW2 defaults to R7 at 13.13.13.1. Unverified other upstream next hops were removed from default routes.
- **R7:** One NAT ACL and overload rule replaces overlapping NAT policies. Equal-cost static routes to each VLAN remain through both distribution switches; test return paths and failover.
- **VLANs:** Explicit VLAN definitions added to all switches. Access-port assignments follow the provided configurations.

## Configuration files

- `configs/DSW1.cfg`, `configs/DSW2.cfg` — multilayer distribution switches
- `configs/ASW1.cfg` to `configs/ASW4.cfg` — access switches
- `configs/R7.cfg` — NAT edge router

These are **cleaned, proposed configurations**, not byte-for-byte backups. Original IOS banners, command prompts, repeated terminal output, and platform defaults were removed. Some intended design corrections and explanatory interface descriptions were added.

## Verification (not yet performed on corrected configs)

Run the commands in `verification/COMMANDS.md` and save actual output or screenshots. Test client DHCP leases, inter-VLAN reachability, NAT connectivity, HSRP failover, MST root placement, and Port-channel state before describing the project as fully validated.

## Known limitations

1. No access-switch cabling diagram or interface-status output was provided; validate trunks and VLAN propagation.
2. R7's WAN DHCP lease, upstream gateway, and Internet access were not verified.
3. R7's equal-cost return routes may need adjustment after failover testing.
4. DSW1 is a single DHCP server; DHCP service will not survive its failure without another solution.
5. Original configs used the same HSRP group 10 on every VLAN; corrected groups match VLAN IDs for readability, but group-number reuse across separate VLANs is generally allowed.
6. `ip http server` and `ip http secure-server` were omitted from the cleaned configurations; secure management, authentication, and management ACLs must be configured separately.
7. IOSv/IOSvL2 platform and licensing restrictions apply; no Cisco software images are distributed.

## License / usage

This repository contains configuration examples and documentation only. Use Cisco software according to its license terms. Do not deploy the example configuration to production without review.
