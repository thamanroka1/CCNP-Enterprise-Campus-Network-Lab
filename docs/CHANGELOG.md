# Corrections from supplied running-configs

- DSW1/DSW2: `ip routing` explicitly enabled; VLANs 10–80 explicitly created.
- DSW1/DSW2: HSRP priorities redistributed by VLAN and groups changed to VLAN numbers.
- DSW1: DHCP pools retained with address exclusions; DSW2: duplicate DHCP pools removed.
- DSW1/DSW2: unverified static default next hops removed, known R7 next hops retained.
- Access switches: original access/trunk port roles preserved, explicit VLANs added; PortFast and BPDU Guard added on designated endpoint access ports (verify these are not connected to switches).
- R7: overlapping NAT overload statements consolidated; routes to VLANs retained.
- Removed repeated terminal prompts, banners, boilerplate and disabled default services from example extracts.
- No command outputs, test screenshots or verified connectivity were supplied for the corrected design.
