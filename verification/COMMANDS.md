# Verification checklist — pending

Do not present commands as test results. Capture outputs after loading the proposed configurations.

## DSW1 and DSW2
```text
show ip interface brief
show vlan brief
show interfaces trunk
show etherchannel summary
show spanning-tree mst
show standby brief
show ip route
show ip dhcp pool
show ip dhcp binding
```

## ASW1–ASW4
```text
show vlan brief
show interfaces trunk
show spanning-tree mst
```

## R7
```text
show ip interface brief
show ip route
show ip nat translations
show ip nat statistics
show access-lists
```

## Functional tests
1. Verify VLAN 10–40 HSRP active on DSW1 and VLAN 50–80 active on DSW2.
2. Verify MST instance 1 root is DSW1 and instance 2 root is DSW2.
3. Confirm Port-channel1 members bundle and all required trunks carry VLANs.
4. Obtain DHCP leases from a client in each VLAN; check excluded addresses.
5. Ping HSRP VIP and test inter-VLAN routing from clients.
6. Test WAN access and observe NAT translations on R7.
7. Shut down one distribution uplink or switch in a controlled test and measure connectivity changes.
8. Test behavior when DSW1 DHCP server is unavailable; document the limitation.
