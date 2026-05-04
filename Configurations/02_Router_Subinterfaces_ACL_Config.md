# Router Subinterfaces and Guest Isolation ACL Configuration

Device:

```text
SC-CORE-R1
```

Purpose:

This configuration sets up router-on-a-stick inter-VLAN routing and applies an ACL to isolate the guest network from internal SteenCorp resources.

---

## Gateway Plan

| VLAN / Network | Router Interface | Gateway IP |
|---|---|---|
| Corporate VLAN 10 | `G0/0.10` | `192.168.10.1` |
| Guest VLAN 20 | `G0/0.20` | `192.168.20.1` |
| Server VLAN 30 | `G0/0.30` | `192.168.30.1` |
| Simulated Internet/Test Network | `G0/1` | `203.0.113.1` |

---

## Router-on-a-Stick Configuration

```cisco
enable
configure terminal

hostname SC-CORE-R1

interface gigabitEthernet0/0
 description Trunk_to_SC-ACCESS-SW1
 no shutdown
exit

interface gigabitEthernet0/0.10
 description Gateway_for_SteenCorp_Corporate_VLAN10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet0/0.20
 description Gateway_for_SteenCorp_Guest_VLAN20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
exit

interface gigabitEthernet0/0.30
 description Gateway_for_SteenCorp_Server_VLAN30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
exit

interface gigabitEthernet0/1
 description Simulated_Internet_Test_Network
 ip address 203.0.113.1 255.255.255.0
 no shutdown
exit

end
write memory
```

---

## Guest Isolation ACL Configuration

```cisco
enable
configure terminal

ip access-list extended GUEST_ISOLATION
 remark Block Guest VLAN from Corporate LAN
 deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 remark Block Guest VLAN from Internal Server Network
 deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
 remark Allow Guest VLAN to reach simulated internet/test network
 permit ip 192.168.20.0 0.0.0.255 any
exit

interface gigabitEthernet0/0.20
 description Gateway_for_SteenCorp_Guest_VLAN20
 ip access-group GUEST_ISOLATION in
exit

end
write memory
```

---

## Verification Commands

```cisco
show ip interface brief
show access-lists
show running-config interface gigabitEthernet0/0.20
```

---

## What This Configuration Does

- Creates router subinterfaces for VLAN 10, VLAN 20, and VLAN 30
- Assigns each VLAN its own default gateway
- Uses 802.1Q tagging so the router can identify VLAN traffic
- Allows routing between VLANs before access rules are applied
- Blocks Guest VLAN traffic from reaching the Corporate LAN
- Blocks Guest VLAN traffic from reaching the Server Network
- Allows Guest VLAN traffic to reach the simulated internet/test network
