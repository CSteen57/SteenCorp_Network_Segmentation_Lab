# Switch VLAN and Trunk Configuration

Device:

```text
SC-ACCESS-SW1
```

Purpose:

This configuration creates the SteenCorp VLANs, assigns access ports to the correct devices, and configures the router-facing switch port as a trunk.

---

## VLAN and Port Plan

| Port | Connected Device | VLAN / Role |
|---|---|---|
| `Fa0/1` | `SC-CORE-R1` | Trunk link |
| `Fa0/2` | `SC-CORP-WK01` | VLAN 10 |
| `Fa0/3` | `SC-GUEST-CLIENT01` | VLAN 20 |
| `Fa0/4` | `SC-INTERNAL-SRV01` | VLAN 30 |

---

## Configuration

```cisco
enable
configure terminal

hostname SC-ACCESS-SW1

vlan 10
 name SteenCorp_Corporate
exit

vlan 20
 name SteenCorp_Guest
exit

vlan 30
 name SteenCorp_Servers
exit

interface fastEthernet0/1
 description Trunk_to_SC-CORE-R1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 no shutdown
exit

interface fastEthernet0/2
 description SC-CORP-WK01
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown
exit

interface fastEthernet0/3
 description SC-GUEST-CLIENT01
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown
exit

interface fastEthernet0/4
 description SC-INTERNAL-SRV01
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
 no shutdown
exit

end
write memory
```

---

## Verification Commands

```cisco
show vlan brief
show interfaces trunk
```

---

## What This Configuration Does

- Creates VLAN 10 for trusted corporate devices
- Creates VLAN 20 for guest/untrusted devices
- Creates VLAN 30 for internal server resources
- Assigns workstation and server ports to the correct VLANs
- Configures the router-facing switch port as a trunk
- Allows VLANs 10, 20, and 30 across the trunk link
