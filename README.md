# Computer Networks Lab - Complete Command Reference

A comprehensive guide to all commands used in ENCS4130 Computer Networks Laboratory experiments at Birzeit University.

---

## Table of Contents
- [Router Basic Commands](#router-basic-commands)
- [Router Interface Configuration](#router-interface-configuration)
- [Static Routing](#static-routing)
- [Dynamic Routing Protocols](#dynamic-routing-protocols)
- [DHCP Configuration](#dhcp-configuration)
- [VLAN Configuration](#vlan-configuration)
- [Access Control Lists (ACLs)](#access-control-lists-acls)
- [Network Troubleshooting Commands](#network-troubleshooting-commands)
- [OSI Model Layers](#osi-model-layers)
- [Network Devices by Layer](#network-devices-by-layer)
- [Routing Protocols Comparison](#routing-protocols-comparison)
- [IP Addressing Classes](#ip-addressing-classes)
- [Subnet Mask & CIDR Reference](#subnet-mask--cidr-reference)
- [Common Network Protocols & Ports](#common-network-protocols--ports)
- [Cable Types Comparison](#cable-types-comparison)
- [VLAN Configuration Comparison](#vlan-configuration-comparison)
- [ACL Types Comparison](#acl-types-comparison)
- [Administrative Distance Reference](#administrative-distance-reference)

---

## Router Basic Commands

### Router Modes

Cisco routers have different command modes with increasing privilege levels:

```bash
# User EXEC Mode (view only, limited access)
Router>

# Enter Privileged EXEC Mode
Router> enable
Router#

# Enter Global Configuration Mode
Router# configure terminal
Router(config)#

# Exit to previous mode
Router(config)# exit
Router#

# Return to Privileged EXEC Mode from any config mode
Router(config-if)# end
Router#
```

**Example Flow:**
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# exit
R1#
```

### Setting Hostname

Changes the router's display name:

```bash
Router(config)# hostname <NAME>
```

**Example:**
```bash
Router(config)# hostname RouterA
RouterA(config)#
```

### Setting Clock

Configure the system date and time:

```bash
Router# clock set <HH:MM:SS> <DAY> <MONTH> <YEAR>
```

**Example:**
```bash
Router# clock set 10:30:10 28 January 2020
```

### Password Configuration

#### Enable Password (Privileged EXEC Mode)

```bash
# Plain text password (less secure)
Router(config)# enable password <PASSWORD>

# Encrypted password (more secure, recommended)
Router(config)# enable secret <PASSWORD>
```

**Example:**
```bash
Router(config)# enable secret Cisco123
```

#### Console Password

Secures console port access:

```bash
Router(config)# line console 0
Router(config-line)# password <PASSWORD>
Router(config-line)# login
Router(config-line)# exit
```

**Example:**
```bash
Router(config)# line console 0
Router(config-line)# password admin123
Router(config-line)# login
Router(config-line)# exit
```

#### Telnet/VTY Password

Secures remote access via Telnet:

```bash
Router(config)# line vty 0 4
Router(config-line)# password <PASSWORD>
Router(config-line)# login
Router(config-line)# exit
```

**Example:**
```bash
Router(config)# line vty 0 4
Router(config-line)# password telnet456
Router(config-line)# login
Router(config-line)# exit
```

---

## Router Interface Configuration

### Accessing an Interface

```bash
Router(config)# interface <TYPE> <SLOT>/<PORT>
Router(config-if)#
```

**Example:**
```bash
# FastEthernet interface
Router(config)# interface FastEthernet0/0
Router(config-if)#

# Serial interface
Router(config)# interface Serial2/0
Router(config-if)#

# GigabitEthernet interface
Router(config)# interface GigabitEthernet0/0
Router(config-if)#
```

### Assigning IP Address

```bash
Router(config-if)# ip address <IP-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
```

### Activating an Interface

Interfaces are administratively down by default:

```bash
Router(config-if)# no shutdown
```

**Example:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
```

### Serial Interface Clock Rate (DCE side only)

```bash
Router(config-if)# clock rate <RATE-IN-BPS>
```

**Example:**
```bash
Router(config)# interface Serial2/0
Router(config-if)# clock rate 64000
```

### Interface Bandwidth

```bash
Router(config-if)# bandwidth <BANDWIDTH-IN-KILOBITS>
```

**Example:**
```bash
Router(config-if)# bandwidth 1544
```

### Creating Loopback Interface

Virtual interface that never goes down (used for router ID, testing):

```bash
Router(config)# interface loopback <LOOPBACK-ID>
Router(config-if)# ip address <IP-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Router(config)# interface loopback 0
Router(config-if)# ip address 172.16.0.1 255.255.255.0
```

---

## Static Routing

### Adding Static Routes

```bash
Router(config)# ip route <DESTINATION-NETWORK> <MASK> <NEXT-HOP-ADDRESS>
```

**Example:**
```bash
# Route to 192.168.2.0/24 via next-hop 192.168.1.2
Router(config)# ip route 192.168.2.0 255.255.255.0 192.168.1.2
```

**Complete Configuration Example:**

```
Network Topology:
PC0 (192.168.0.2) -- Router0 (Fa0/0: 192.168.0.1, Se2/0: 192.168.2.1) -- Router1 (Se2/0: 192.168.2.2, Fa0/0: 192.168.1.1) -- PC1 (192.168.1.2)
```

**Router0 Configuration:**
```bash
Router0(config)# ip route 192.168.1.0 255.255.255.0 192.168.2.2
```

**Router1 Configuration:**
```bash
Router1(config)# ip route 192.168.0.0 255.255.255.0 192.168.2.1
```

---

## Dynamic Routing Protocols

### RIP (Routing Information Protocol)

#### RIPv1 Configuration

```bash
Router(config)# router rip
Router(config-router)# version 1
Router(config-router)# network <NETWORK-ID>
```

**Example:**
```bash
Router(config)# router rip
Router(config-router)# version 1
Router(config-router)# network 192.168.1.0
Router(config-router)# network 192.168.2.0
```

#### RIPv2 Configuration

```bash
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network <NETWORK-ID>
Router(config-router)# no auto-summary
```

**Example:**
```bash
Router(config)# router rip
Router(config-router)# version 2
Router(config-router)# network 192.168.0.0
Router(config-router)# network 10.0.0.0
Router(config-router)# no auto-summary
```

### EIGRP (Enhanced Interior Gateway Routing Protocol)

```bash
Router(config)# router eigrp <AS-NUMBER>
Router(config-router)# network <NETWORK-ID> [<WILDCARD-MASK>]
Router(config-router)# no auto-summary
```

**Example:**
```bash
Router(config)# router eigrp 100
Router(config-router)# network 192.168.0.0 0.0.0.255
Router(config-router)# network 10.0.10.0 0.0.0.3
Router(config-router)# no auto-summary
```

### OSPF (Open Shortest Path First)

#### Basic OSPF Configuration

```bash
Router(config)# router ospf <PROCESS-ID>
Router(config-router)# network <NETWORK-ID> <WILDCARD-MASK> area <AREA-ID>
```

**Example:**
```bash
Router(config)# router ospf 1
Router(config-router)# network 192.168.0.0 0.0.0.255 area 0
Router(config-router)# network 10.0.10.0 0.0.0.3 area 0
```

#### OSPF Router ID

```bash
Router(config-router)# router-id <A.B.C.D>
```

**Example:**
```bash
Router(config)# router ospf 1
Router(config-router)# router-id 1.1.1.1
```

#### OSPF Route Summarization

```bash
Router(config-router)# area <AREA-ID> range <SUMMARY-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Router(config)# router ospf 1
Router(config-router)# area 1 range 172.16.0.0 255.255.248.0
```

#### Changing OSPF Cost

```bash
Router(config-if)# bandwidth <BANDWIDTH-IN-KILOBITS>
```

**Example:**
```bash
# Set cost to 5 by adjusting bandwidth to 20 Mbps
Router(config)# interface Serial2/0
Router(config-if)# bandwidth 20000
```

### BGP (Border Gateway Protocol)

#### Basic BGP Configuration

```bash
Router(config)# router bgp <AS-NUMBER>
Router(config-router)# neighbor <IP-ADDRESS> remote-as <AS-NUMBER>
```

**Example:**
```bash
# Router in AS 100 peering with router in AS 200
Router(config)# router bgp 100
Router(config-router)# neighbor 192.168.0.6 remote-as 200
```

#### BGP Timers

```bash
Router(config-router)# timers bgp <KEEPALIVE> <HOLD-TIME>
```

**Example:**
```bash
Router(config)# router bgp 100
Router(config-router)# timers bgp 30 90
```

#### Route Redistribution

**Redistributing BGP into OSPF:**
```bash
Router(config)# router ospf 1
Router(config-router)# redistribute bgp 100 subnets
```

**Redistributing OSPF into BGP:**
```bash
Router(config)# router bgp 100
Router(config-router)# redistribute ospf 1
```

**Redistributing BGP into EIGRP:**
```bash
Router(config)# router eigrp 50
Router(config-router)# redistribute bgp 200 metric 1544 2000 255 1 1500
```

**Redistributing EIGRP into BGP:**
```bash
Router(config)# router bgp 200
Router(config-router)# redistribute eigrp 50
```

---

## DHCP Configuration

### DHCP Server on Router

#### Exclude IP Addresses

```bash
Router(config)# ip dhcp excluded-address <FIRST-ADDRESS> <LAST-ADDRESS>
```

**Example:**
```bash
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
```

#### Create DHCP Pool

```bash
Router(config)# ip dhcp pool <POOL-NAME>
Router(dhcp-config)# network <NETWORK-ID> <SUBNET-MASK>
Router(dhcp-config)# default-router <GATEWAY-IP>
Router(dhcp-config)# dns-server <DNS-SERVER-IP>
Router(dhcp-config)# exit
```

**Example:**
```bash
Router(config)# ip dhcp pool LAN1
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 192.168.2.2
Router(dhcp-config)# exit
```

#### Enable DHCP Service

```bash
Router(config)# service dhcp
```

### DHCP Relay (IP Helper)

Forward DHCP requests to a remote DHCP server:

```bash
Router(config)# interface <INTERFACE>
Router(config-if)# ip helper-address <DHCP-SERVER-IP>
```

**Example:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# ip helper-address 192.168.1.2
```

---

## VLAN Configuration

### Creating VLANs

#### On Switch

```bash
Switch(config)# vlan <VLAN-NUMBER>
Switch(config-vlan)# exit
```

**Example:**
```bash
Switch(config)# vlan 10
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# exit
```

### Assigning Ports to VLANs

#### Access Port (Single VLAN)

```bash
Switch(config)# interface <INTERFACE>
Switch(config-if)# switchport access vlan <VLAN-NUMBER>
```

**Example:**
```bash
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport access vlan 10
```

#### Range of Interfaces

```bash
Switch(config)# interface range <TYPE> <SLOT>/<START-PORT> - <END-PORT>
Switch(config-if-range)# switchport access vlan <VLAN-NUMBER>
```

**Example:**
```bash
Switch(config)# interface range FastEthernet0/1 - 5
Switch(config-if-range)# switchport access vlan 10
```

### Trunk Configuration

#### Layer 2 Switch Trunk

```bash
Switch(config)# interface <INTERFACE>
Switch(config-if)# switchport mode trunk
```

**Example:**
```bash
Switch(config)# interface FastEthernet0/24
Switch(config-if)# switchport mode trunk
```

#### Layer 3 Switch Trunk

```bash
Switch(config)# interface <INTERFACE>
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
```

**Example:**
```bash
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
```

### Router-on-a-Stick (Sub-interfaces)

```bash
# Enable physical interface
Router(config)# interface <INTERFACE>
Router(config-if)# no shutdown

# Create sub-interface
Router(config)# interface <INTERFACE>.<SUB-INTERFACE-NUMBER>
Router(config-subif)# encapsulation dot1Q <VLAN-ID>
Router(config-subif)# ip address <IP-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# no shutdown

Router(config)# interface FastEthernet0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0

Router(config)# interface FastEthernet0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
```

### Layer 3 Switch Configuration

#### Enable IP Routing

```bash
Switch(config)# ip routing
```

#### Convert Port to Routed Port

```bash
Switch(config)# interface <INTERFACE>
Switch(config-if)# no switchport
Switch(config-if)# ip address <IP-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Switch(config)# interface FastEthernet0/1
Switch(config-if)# no switchport
Switch(config-if)# ip address 192.168.0.1 255.255.255.252
```

#### Create SVI (Switch Virtual Interface)

```bash
Switch(config)# interface vlan <VLAN-NUMBER>
Switch(config-if)# ip address <IP-ADDRESS> <SUBNET-MASK>
```

**Example:**
```bash
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0

Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
```

---

## Access Control Lists (ACLs)

### Standard ACL (1-99)

Filters based on **source IP only**.

#### Creating Standard ACL

```bash
Router(config)# access-list <ACL-NUMBER> <permit|deny> <host|source wildcard|any>
```

**Example 1: Block specific host**
```bash
Router(config)# access-list 10 deny host 192.168.10.2
Router(config)# access-list 10 permit any
```

**Example 2: Block entire network**
```bash
Router(config)# access-list 10 deny 192.168.10.0 0.0.0.255
Router(config)# access-list 10 permit any
```

**Example 3: Allow only specific network**
```bash
Router(config)# access-list 10 permit 192.168.60.0 0.0.0.255
# Implicit deny all at the end
```

#### Applying Standard ACL

```bash
Router(config)# interface <INTERFACE>
Router(config-if)# ip access-group <ACL-NUMBER> <in|out>
```

**Example:**
```bash
Router(config)# interface FastEthernet1/0
Router(config-if)# ip access-group 10 out
```

### Extended ACL (100-199)

Filters based on **source, destination, protocol, and port**.

#### Creating Extended ACL

```bash
Router(config)# access-list <ACL-NUMBER> <permit|deny> <protocol> <source wildcard|any> <destination wildcard|any> [eq <port>]
```

**Example 1: Block HTTP from specific host to server**
```bash
Router(config)# access-list 101 deny tcp host 192.168.10.2 host 192.168.20.4 eq 80
Router(config)# access-list 101 permit ip any any
```

**Example 2: Block Telnet access**
```bash
Router(config)# access-list 105 deny tcp any host 192.168.30.2 eq 23
Router(config)# access-list 105 permit ip any any
```

**Example 3: Allow only specific PC to reach another**
```bash
Router(config)# access-list 102 permit ip host 192.168.40.2 host 192.168.10.3
# Implicit deny all others
```

#### Applying Extended ACL

```bash
Router(config)# interface <INTERFACE>
Router(config-if)# ip access-group <ACL-NUMBER> <in|out>
```

**Example:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# ip access-group 101 in
```

### Common Port Numbers for ACLs

| Service | Port | Protocol | ACL Keyword |
|---------|------|----------|-------------|
| HTTP | 80 | TCP | `www` |
| HTTPS | 443 | TCP | `https` |
| FTP | 21 | TCP | `ftp` |
| Telnet | 23 | TCP | `telnet` |
| SSH | 22 | TCP | `ssh` |
| SMTP | 25 | TCP | `smtp` |
| DNS | 53 | TCP/UDP | `domain` |
| POP3 | 110 | TCP | `pop3` |

**Example using port name:**
```bash
Router(config)# access-list 101 deny tcp host 192.168.1.2 host 172.16.100.100 eq www
```

---

## Network Troubleshooting Commands

### Show Commands

#### Show Running Configuration

```bash
Router# show running-config
```

#### Show IP Interface Brief

Displays summary of all interfaces:

```bash
Router# show ip interface brief
```

#### Show IP Route

Displays routing table:

```bash
Router# show ip route
```

**Understanding output:**
- `C` = Connected
- `S` = Static
- `R` = RIP
- `D` = EIGRP
- `O` = OSPF
- `B` = BGP

#### Show IP Protocols

Shows active routing protocols:

```bash
Router# show ip protocols
```

#### Show OSPF Neighbor

```bash
Router# show ip ospf neighbor
```

#### Show OSPF Interface

```bash
Router# show ip ospf interface
```

#### Show BGP Summary

```bash
Router# show ip bgp summary
```

#### Show BGP Neighbors

```bash
Router# show ip bgp neighbors
```

#### Show VLANs

```bash
Switch# show vlan
```

#### Show MAC Address Table

```bash
Switch# show mac address-table dynamic
```

#### Show Access Lists

```bash
Router# show access-lists
```

### Debug Commands

#### Debug EIGRP Packets

```bash
Router# debug eigrp packets
```

**Disable debugging:**
```bash
Router# no debug eigrp packets
```

#### Turn off all debugging

```bash
Router# undebug all
```

### PC Commands (Windows)

#### IPCONFIG

Display IP configuration:

```cmd
ipconfig
ipconfig /all
```

#### PING

Test connectivity:

```cmd
ping <IP-ADDRESS>
ping <HOSTNAME>

# Examples
ping 192.168.1.1
ping google.com
ping 192.168.1.1 -n 5         # Send 5 packets
ping 192.168.1.1 -f           # Don't fragment
```

#### TRACERT

Trace route to destination:

```cmd
tracert <IP-ADDRESS>
tracert <HOSTNAME>

# Example
tracert ritaj.birzeit.edu
```

#### NETSTAT

Display network statistics and connections:

```cmd
netstat -an    # Show all connections and ports
```

---

## Configuration Management

### Saving Configuration

#### Save Running Config to Startup Config

```bash
Router# copy running-config startup-config
# Or the shortcut:
Router# copy run start
# Or simply:
Router# write
```

#### Reload Configuration from NVRAM

```bash
Router# reload
```

#### Erase Startup Configuration

```bash
Router# erase startup-config
Router# reload
```

### Configuration Help

#### Context-sensitive Help

```bash
Router# ?                    # List all commands
Router# c?                   # Commands starting with 'c'
Router# clock ?              # Options for clock command
Router# clock set ?          # Next parameter options
```

#### Command Completion

Press `Tab` key to auto-complete commands.

---

## Wildcard Mask Reference

Wildcard masks are inverse of subnet masks (used in OSPF, EIGRP, ACLs):

| CIDR | Subnet Mask | Wildcard Mask |
|------|-------------|---------------|
| /8 | 255.0.0.0 | 0.255.255.255 |
| /16 | 255.255.0.0 | 0.0.255.255 |
| /24 | 255.255.255.0 | 0.0.0.255 |
| /25 | 255.255.255.128 | 0.0.0.127 |
| /26 | 255.255.255.192 | 0.0.0.63 |
| /27 | 255.255.255.224 | 0.0.0.31 |
| /28 | 255.255.255.240 | 0.0.0.15 |
| /29 | 255.255.255.248 | 0.0.0.7 |
| /30 | 255.255.255.252 | 0.0.0.3 |
| /32 | 255.255.255.255 | 0.0.0.0 |

**Calculation:** `Wildcard Mask = 255.255.255.255 - Subnet Mask`

---

## Complete Configuration Examples

### Example 1: Basic Router Setup with Static Routing

**Topology:**
```
PC0 (192.168.1.2/24) --- Router0 --- Router1 --- PC1 (192.168.2.2/24)
                      Se2/0     Se2/0
                   (192.168.3.1) (192.168.3.2)
```

**Router0:**
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R0
R0(config)# interface FastEthernet0/0
R0(config-if)# ip address 192.168.1.1 255.255.255.0
R0(config-if)# no shutdown
R0(config-if)# exit

R0(config)# interface Serial2/0
R0(config-if)# ip address 192.168.3.1 255.255.255.0
R0(config-if)# clock rate 64000
R0(config-if)# no shutdown
R0(config-if)# exit

R0(config)# ip route 192.168.2.0 255.255.255.0 192.168.3.2
R0(config)# end
R0# write
```

**Router1:**
```bash
Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# interface FastEthernet0/0
R1(config-if)# ip address 192.168.2.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface Serial2/0
R1(config-if)# ip address 192.168.3.2 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# ip route 192.168.1.0 255.255.255.0 192.168.3.1
R1(config)# end
R1# write
```

### Example 2: OSPF Configuration

**Router Configuration:**
```bash
Router(config)# router ospf 1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 192.168.3.0 0.0.0.255 area 0
Router(config-router)# end
Router# write
```

### Example 3: VLAN with Router-on-a-Stick

**Switch Configuration:**
```bash
Switch(config)# vlan 10
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# exit

Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface FastEthernet0/2
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit

Switch(config)# interface FastEthernet0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# end
Switch# write
```

**Router Configuration:**
```bash
Router(config)# interface FastEthernet0/0
Router(config-if)# no shutdown
Router(config-if)# exit

Router(config)# interface FastEthernet0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
Router(config-subif)# exit

Router(config)# interface FastEthernet0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 192.168.20.1 255.255.255.0
Router(config-subif)# end
Router# write
```

---

## Quick Reference Card

### Most Common Commands

```bash
# Enter privileged mode
enable

# Enter configuration mode
configure terminal

# Set hostname
hostname <NAME>

# Configure interface
interface <TYPE><SLOT>/<PORT>
ip address <IP> <MASK>
no shutdown

# Save configuration
write

# View routing table
show ip route

# View interface status
show ip interface brief

# Test connectivity
ping <IP>

# Static route
ip route <DEST> <MASK> <NEXT-HOP>

# OSPF
router ospf 1
network <NETWORK> <WILDCARD> area 0

# Create VLAN
vlan <NUMBER>

# Trunk port
switchport mode trunk

# Access port
switchport access vlan <NUMBER>

# Standard ACL
access-list <1-99> <permit|deny> <SOURCE>

# Extended ACL
access-list <100-199> <permit|deny> <PROTOCOL> <SRC> <DST> eq <PORT>

# Apply ACL
ip access-group <NUMBER> <in|out>
```

---

## Tips and Best Practices

1. **Always save your configuration**: Use `write` or `copy run start`
2. **Use meaningful hostnames**: Makes network management easier
3. **Document your network**: Keep track of IP addresses and VLANs
4. **Test before deploying ACLs**: ACLs can block legitimate traffic
5. **Place ACLs correctly**:
   - Standard ACLs: Close to destination
   - Extended ACLs: Close to source
6. **Use `no auto-summary`**: In RIPv2 and EIGRP for proper subnet routing
7. **Verify before moving on**: Use `show` commands to verify each step
8. **Use loopback interfaces**: For stable router IDs in OSPF
9. **Remember the implicit deny**: Always add permit statements in ACLs

---

## Common Issues and Solutions

### Issue 1: Interface is down
**Solution:**
```bash
Router(config-if)# no shutdown
```

### Issue 2: Can't ping between VLANs
**Solution:** Configure inter-VLAN routing (Router-on-a-Stick or Layer 3 switch)

### Issue 3: Static route not working
**Solution:** Verify:
- Next-hop is reachable
- Correct network and mask
- Interface is up

### Issue 4: OSPF neighbors not forming
**Solution:** Check:
- Area IDs match
- Hello/Dead timers match
- Network masks match
- No ACLs blocking OSPF

### Issue 5: ACL blocking everything
**Solution:** Remember to add `permit any` at the end:
```bash
Router(config)# access-list 10 permit any
```

---

## Keyboard Shortcuts

| Shortcut | Function |
|----------|----------|
| `Ctrl+A` | Move cursor to beginning of line |
| `Ctrl+E` | Move cursor to end of line |
| `Ctrl+P` or `↑` | Previous command |
| `Ctrl+N` or `↓` | Next command |
| `Ctrl+Z` | Exit to privileged mode |
| `Tab` | Command completion |
| `?` | Context-sensitive help |

---

## Administrative Distance Table

| Route Source | AD Value |
|--------------|----------|
| Connected | 0 |
| Static | 1 |
| EIGRP | 90 |
| IGRP | 100 |
| OSPF | 110 |
| RIP | 120 |
| External EIGRP | 170 |
| iBGP | 200 |
| Unknown | 255 |

---

## Port Numbers Reference

| Service | Port | Protocol |
|---------|------|----------|
| FTP Data | 20 | TCP |
| FTP Control | 21 | TCP |
| SSH | 22 | TCP |
| Telnet | 23 | TCP |
| SMTP | 25 | TCP |
| DNS | 53 | TCP/UDP |
| HTTP | 80 | TCP |
| POP3 | 110 | TCP |
| IMAP | 143 | TCP |
| HTTPS | 443 | TCP |
| SMTP (SSL) | 465 | TCP |
| SMTP (TLS) | 587 | TCP |
| IMAP (SSL) | 993 | TCP |
| POP3 (SSL) | 995 | TCP |

---
# Network Fundamentals - Comprehensive Reference Tables

---

## OSI Model Layers

Complete reference of the 7-layer OSI model with data units and functions:

| Layer | Number | Name | Data Unit | Protocols/Examples | Primary Function | Devices |
|-------|--------|------|-----------|-------------------|------------------|---------|
| **Application** | 7 | Application Layer | **Data** | HTTP, FTP, SMTP, DNS, DHCP, Telnet, SSH | Provides network services to end-user applications | User Applications |
| **Presentation** | 6 | Presentation Layer | **Data** | SSL/TLS, JPEG, GIF, MPEG, ASCII | Data formatting, encryption/decryption, compression | None (Software) |
| **Session** | 5 | Session Layer | **Data** | NetBIOS, RPC, SQL, NFS | Establishes, manages, and terminates sessions | None (Software) |
| **Transport** | 4 | Transport Layer | **Segments** (TCP)<br>**Datagrams** (UDP) | TCP, UDP | End-to-end connections, reliability, flow control | Firewall (Advanced) |
| **Network** | 3 | Network Layer | **Packets** | IP, ICMP, OSPF, EIGRP, RIP, BGP | Logical addressing, routing, path determination | Router, Layer 3 Switch |
| **Data Link** | 2 | Data Link Layer | **Frames** | Ethernet, PPP, HDLC, Frame Relay, MAC, ARP | Physical addressing (MAC), error detection, frame synchronization | Switch, Bridge, NIC |
| **Physical** | 1 | Physical Layer | **Bits** | Ethernet Physical, DSL, USB, Bluetooth | Physical transmission of raw bits over medium | Hub, Repeater, Cables, Modem |

### Layer Groupings:

| Group | Layers | Description |
|-------|--------|-------------|
| **Host Layers** | 7, 6, 5, 4 | Handle data representation, communication between hosts |
| **Media Layers** | 3, 2, 1 | Handle network addressing, media access, physical transmission |
| **Upper Layers** | 7, 6, 5 | Software layers - closest to user |
| **Lower Layers** | 4, 3, 2, 1 | Hardware/software - handle data transport |

---

## Network Devices by Layer

Detailed classification of network devices by OSI layer:

| Device | OSI Layer | Layer Name | Primary Function | Intelligence | Collision/Broadcast Domains |
|--------|-----------|------------|------------------|--------------|----------------------------|
| **Hub** | Layer 1 | Physical | Broadcasts to all ports (dumb device) | None | 1 collision domain, 1 broadcast domain |
| **Repeater** | Layer 1 | Physical | Amplifies/regenerates signal | None | Extends single collision domain |
| **Modem** | Layer 1 | Physical | Modulates/demodulates signals | None | N/A |
| **Cable** | Layer 1 | Physical | Physical medium for transmission | None | N/A |
| **Bridge** | Layer 2 | Data Link | Connects network segments, filters by MAC | Learns MAC addresses | Multiple collision domains, 1 broadcast |
| **Switch** | Layer 2 | Data Link | Forwards frames based on MAC address | Learns MAC, filters traffic | 1 collision domain per port, 1 broadcast |
| **NIC** | Layer 2 | Data Link | Connects device to network | Has unique MAC address | N/A |
| **Access Point** | Layer 2 | Data Link | Wireless connectivity | Broadcasts SSID | 1 collision domain (CSMA/CA) |
| **Router** | Layer 3 | Network | Routes packets between networks | Routing decisions, NAT | Breaks collision & broadcast domains |
| **Layer 3 Switch** | Layer 2 & 3 | Data Link & Network | Switching + Routing capabilities | Routing + Switching | Multiple broadcast domains |
| **Firewall** | Layer 3-7 | Network-Application | Filters traffic, security enforcement | Stateful inspection, ACLs | Breaks broadcast domains |

### Device Comparison:

| Feature | Hub | Switch | Router | Layer 3 Switch |
|---------|-----|--------|--------|----------------|
| **Speed** | Slowest | Fast | Moderate | Fastest |
| **Decision Based On** | None | MAC Address | IP Address | MAC + IP Address |
| **Broadcast Handling** | Floods all ports | Floods within VLAN | Blocks broadcasts | Blocks between VLANs |
| **Port Independence** | No | Yes | Yes | Yes |
| **Routing Capability** | No | No | Yes | Yes |
| **Cost** | Cheapest | Low | Moderate-High | High |
| **Best Use Case** | Legacy/Small networks | LAN switching | WAN connectivity | Enterprise campus |

---

## Routing Protocols Comparison

Comprehensive comparison of all routing protocols covered in the course:

### Overview Table:

| Protocol | Type | Algorithm | Metric | Admin Distance | Hop Limit | Convergence | Classless | Standard |
|----------|------|-----------|--------|----------------|-----------|-------------|-----------|----------|
| **RIPv1** | IGP - Distance Vector | Bellman-Ford | Hop Count | 120 | 15 | Slow | No | Open |
| **RIPv2** | IGP - Distance Vector | Bellman-Ford | Hop Count | 120 | 15 | Slow | Yes | Open |
| **EIGRP** | IGP - Advanced Distance Vector | DUAL | Composite (BW + Delay) | 90 (internal)<br>170 (external) | 255 (default 100) | Fast | Yes | Formerly Cisco (Now Open) |
| **OSPF** | IGP - Link State | Dijkstra (SPF) | Cost (Bandwidth) | 110 | None | Fast | Yes | Open (RFC 2328) |
| **IS-IS** | IGP - Link State | Dijkstra (SPF) | Cost | 115 | None | Fast | Yes | Open |
| **BGP** | EGP - Path Vector | Best Path Selection | Path Attributes | 20 (eBGP)<br>200 (iBGP) | None | Varies | Yes | Open (RFC 4271) |

### Detailed Comparison:

| Feature | RIPv1 | RIPv2 | EIGRP | OSPF | BGP |
|---------|-------|-------|-------|------|-----|
| **Updates** | Broadcast (255.255.255.255) | Multicast (224.0.0.9) | Multicast (224.0.0.10) | Multicast (224.0.0.5, 224.0.0.6) | TCP (Port 179) |
| **Update Type** | Full periodic (30s) | Full periodic (30s) + Triggered | Partial (triggered only) | Triggered (LSAs) | Incremental |
| **VLSM Support** | No | Yes | Yes | Yes | Yes |
| **Subnet Mask in Updates** | No | Yes | Yes | Yes | Yes |
| **Authentication** | No | Plain/MD5 | MD5 | Plain/MD5 | MD5/TCP |
| **Load Balancing** | 6 equal-cost | 6 equal-cost | 32 equal/unequal-cost | 16 equal-cost | Yes (complex) |
| **Scalability** | Small (< 15 hops) | Small (< 15 hops) | Large | Very Large | Internet-scale |
| **Network Hierarchy** | Flat | Flat | Flat | Areas (2-level) | AS-based |
| **Best For** | Very small networks | Small networks | Enterprise (Cisco) | Large enterprise | ISP/Internet |
| **CPU Usage** | Low | Low | Moderate | High | Moderate-High |
| **Memory Usage** | Low | Low | Moderate | High | Very High |
| **Bandwidth Usage** | High (periodic) | High (periodic) | Low (incremental) | Low-Moderate | Low |
| **Convergence Time** | Minutes | Minutes | Seconds | Seconds | Minutes (stability priority) |
| **Loop Prevention** | Hop count, Split horizon | Hop count, Split horizon | DUAL (loop-free) | SPF algorithm | AS Path |

### Protocol Selection Guide:

| Network Size | Network Type | Recommended Protocol | Reason |
|--------------|--------------|---------------------|---------|
| Very Small (< 10 routers) | Simple topology | RIPv2 | Easy to configure, sufficient for small networks |
| Small (10-50 routers) | Single organization | RIPv2 or EIGRP | RIPv2 if open standard needed, EIGRP for better performance |
| Medium (50-200 routers) | Campus/Enterprise | OSPF or EIGRP | OSPF for multi-vendor, EIGRP for Cisco-only with faster convergence |
| Large (200+ routers) | Enterprise/Multi-site | OSPF | Better scalability, hierarchical design, industry standard |
| Very Large (ISP/Multi-AS) | Internet backbone | BGP | Only protocol for inter-AS routing |

### Metric Comparison:

| Protocol | Metric Components | Formula/Calculation | Example |
|----------|------------------|---------------------|---------|
| **RIPv1/v2** | Hop Count | Number of routers | Hop count = 3 |
| **EIGRP** | Bandwidth, Delay, (Reliability, Load, MTU) | [(K1×BW + K3×Delay) × 256] × [K5/(K4+Reliability)] | Default uses BW + Delay |
| **OSPF** | Cost (Bandwidth) | Reference BW / Interface BW | 100 Mbps / 10 Mbps = 10 |
| **BGP** | Path Attributes | Weight, Local Pref, AS Path, Origin, MED, etc. | Multi-stage selection |

---

## IP Addressing Classes

Complete IP address classification with ranges and usage:

| Class | First Octet Range | First Bits | Network Bits | Host Bits | Default Subnet Mask | CIDR | Networks | Hosts per Network | Usage |
|-------|------------------|------------|--------------|-----------|-------------------|------|----------|------------------|--------|
| **A** | 1 - 126 | 0 | 8 | 24 | 255.0.0.0 | /8 | 126 | 16,777,214 | Very large organizations |
| **B** | 128 - 191 | 10 | 16 | 16 | 255.255.0.0 | /16 | 16,384 | 65,534 | Medium to large organizations |
| **C** | 192 - 223 | 110 | 24 | 8 | 255.255.255.0 | /24 | 2,097,152 | 254 | Small organizations |
| **D** | 224 - 239 | 1110 | N/A | N/A | N/A | N/A | N/A | N/A | Multicast groups |
| **E** | 240 - 255 | 1111 | N/A | N/A | N/A | N/A | N/A | N/A | Experimental/Reserved |

### Special IP Addresses:

| IP Address/Range | Purpose | Description |
|-----------------|---------|-------------|
| **0.0.0.0** | Default route | Represents "any" network |
| **127.0.0.0/8** | Loopback | Local host (127.0.0.1 most common) |
| **10.0.0.0/8** | Private (Class A) | RFC 1918 private addressing |
| **172.16.0.0/12** | Private (Class B) | RFC 1918 private addressing |
| **192.168.0.0/16** | Private (Class C) | RFC 1918 private addressing |
| **169.254.0.0/16** | APIPA | Automatic Private IP Addressing |
| **224.0.0.0/4** | Multicast | Multicast group addresses |
| **255.255.255.255** | Broadcast | Limited broadcast address |

### Private vs Public IP:

| Aspect | Private IP | Public IP |
|--------|-----------|-----------|
| **Routable on Internet** | No | Yes |
| **Assignment** | Organization internally | ISP/IANA |
| **Cost** | Free | Purchased/Leased |
| **Uniqueness** | Within organization only | Globally unique |
| **Translation Required** | Yes (NAT) | No |
| **Example Ranges** | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 | All other valid IPs |

---

## Subnet Mask & CIDR Reference

Complete subnetting reference table:

| CIDR | Subnet Mask | Wildcard Mask | Binary (Last Octet) | Network Bits | Host Bits | Total IPs | Usable Hosts | Networks (Class C) |
|------|-------------|---------------|-------------------|--------------|-----------|-----------|--------------|-------------------|
| /8 | 255.0.0.0 | 0.255.255.255 | 00000000 | 8 | 24 | 16,777,216 | 16,777,214 | 1 |
| /16 | 255.255.0.0 | 0.0.255.255 | 00000000 | 16 | 16 | 65,536 | 65,534 | 256 |
| /17 | 255.255.128.0 | 0.0.127.255 | 10000000 | 17 | 15 | 32,768 | 32,766 | 128 |
| /18 | 255.255.192.0 | 0.0.63.255 | 11000000 | 18 | 14 | 16,384 | 16,382 | 64 |
| /19 | 255.255.224.0 | 0.0.31.255 | 11100000 | 19 | 13 | 8,192 | 8,190 | 32 |
| /20 | 255.255.240.0 | 0.0.15.255 | 11110000 | 20 | 12 | 4,096 | 4,094 | 16 |
| /21 | 255.255.248.0 | 0.0.7.255 | 11111000 | 21 | 11 | 2,048 | 2,046 | 8 |
| /22 | 255.255.252.0 | 0.0.3.255 | 11111100 | 22 | 10 | 1,024 | 1,022 | 4 |
| /23 | 255.255.254.0 | 0.0.1.255 | 11111110 | 23 | 9 | 512 | 510 | 2 |
| /24 | 255.255.255.0 | 0.0.0.255 | 00000000 | 24 | 8 | 256 | 254 | 1 |
| /25 | 255.255.255.128 | 0.0.0.127 | 10000000 | 25 | 7 | 128 | 126 | 0.5 |
| /26 | 255.255.255.192 | 0.0.0.63 | 11000000 | 26 | 6 | 64 | 62 | 0.25 |
| /27 | 255.255.255.224 | 0.0.0.31 | 11100000 | 27 | 5 | 32 | 30 | 0.125 |
| /28 | 255.255.255.240 | 0.0.0.15 | 11110000 | 28 | 4 | 16 | 14 | 0.0625 |
| /29 | 255.255.255.248 | 0.0.0.7 | 11111000 | 29 | 3 | 8 | 6 | 0.03125 |
| /30 | 255.255.255.252 | 0.0.0.3 | 11111100 | 30 | 2 | 4 | 2 | 0.015625 |
| /31 | 255.255.255.254 | 0.0.0.1 | 11111110 | 31 | 1 | 2 | 2* | Point-to-point only |
| /32 | 255.255.255.255 | 0.0.0.0 | 11111111 | 32 | 0 | 1 | 1* | Single host |

**Note:** /31 and /32 are special cases (RFC 3021 for /31)

### Quick Subnetting Guide:

| Hosts Needed | Recommended CIDR | Usable Hosts | Common Use Case |
|--------------|-----------------|--------------|-----------------|
| 2 | /30 | 2 | Point-to-point links (router-to-router) |
| 6 | /29 | 6 | Very small office |
| 14 | /28 | 14 | Small office segment |
| 30 | /27 | 30 | Small department |
| 62 | /26 | 62 | Medium department |
| 126 | /25 | 126 | Large department |
| 254 | /24 | 254 | Standard subnet |
| 510 | /23 | 510 | Large network |
| 1,022 | /22 | 1,022 | Very large network |

---

## Common Network Protocols & Ports

Comprehensive protocol reference with ports and descriptions:

### Application Layer Protocols:

| Protocol | Port(s) | Transport | Full Name | Description | OSI Layer |
|----------|---------|-----------|-----------|-------------|-----------|
| **HTTP** | 80 | TCP | Hypertext Transfer Protocol | Web browsing (unencrypted) | 7 |
| **HTTPS** | 443 | TCP | HTTP Secure | Secure web browsing (SSL/TLS) | 7 |
| **FTP** | 20, 21 | TCP | File Transfer Protocol | File transfer (20=data, 21=control) | 7 |
| **FTPS** | 989, 990 | TCP | FTP Secure | Secure file transfer | 7 |
| **SFTP** | 22 | TCP | SSH File Transfer Protocol | Secure file transfer over SSH | 7 |
| **TFTP** | 69 | UDP | Trivial File Transfer Protocol | Simple file transfer | 7 |
| **SSH** | 22 | TCP | Secure Shell | Secure remote access | 7 |
| **Telnet** | 23 | TCP | Teletype Network | Remote access (unencrypted) | 7 |
| **SMTP** | 25 | TCP | Simple Mail Transfer Protocol | Email sending | 7 |
| **SMTPS** | 465, 587 | TCP | SMTP Secure | Secure email sending (465=SSL, 587=TLS) | 7 |
| **POP3** | 110 | TCP | Post Office Protocol v3 | Email retrieval (downloads and deletes) | 7 |
| **POP3S** | 995 | TCP | POP3 Secure | Secure email retrieval | 7 |
| **IMAP** | 143 | TCP | Internet Message Access Protocol | Email retrieval (keeps on server) | 7 |
| **IMAPS** | 993 | TCP | IMAP Secure | Secure email retrieval | 7 |
| **DNS** | 53 | TCP/UDP | Domain Name System | Domain name resolution | 7 |
| **DHCP** | 67, 68 | UDP | Dynamic Host Configuration Protocol | Automatic IP assignment (67=server, 68=client) | 7 |
| **SNMP** | 161, 162 | UDP | Simple Network Management Protocol | Network management (161=agent, 162=trap) | 7 |
| **NTP** | 123 | UDP | Network Time Protocol | Time synchronization | 7 |
| **LDAP** | 389 | TCP | Lightweight Directory Access Protocol | Directory services | 7 |
| **LDAPS** | 636 | TCP | LDAP Secure | Secure directory services | 7 |
| **RDP** | 3389 | TCP | Remote Desktop Protocol | Windows remote desktop | 7 |
| **VNC** | 5900+ | TCP | Virtual Network Computing | Remote desktop access | 7 |
| **SIP** | 5060, 5061 | TCP/UDP | Session Initiation Protocol | VoIP call setup | 5/7 |
| **H.323** | 1720 | TCP | H.323 Protocol | VoIP/video conferencing | 5 |

### Transport Layer Protocols:

| Protocol | Port | Description | Characteristics |
|----------|------|-------------|-----------------|
| **TCP** | N/A | Transmission Control Protocol | Connection-oriented, reliable, ordered delivery, flow control |
| **UDP** | N/A | User Datagram Protocol | Connectionless, unreliable, fast, no overhead |

### Network Layer Protocols:

| Protocol | Protocol Number | Full Name | Description |
|----------|----------------|-----------|-------------|
| **IP** | N/A | Internet Protocol | Logical addressing and routing |
| **ICMP** | 1 | Internet Control Message Protocol | Error reporting, diagnostics (ping, traceroute) |
| **IGMP** | 2 | Internet Group Management Protocol | Multicast group management |
| **IPsec** | 50, 51 | Internet Protocol Security | IP packet encryption (50=ESP, 51=AH) |
| **GRE** | 47 | Generic Routing Encapsulation | Tunneling protocol |

### Routing Protocol Ports/Protocols:

| Protocol | Port/Protocol | Type | Update Method |
|----------|--------------|------|---------------|
| **RIP** | UDP 520 | Distance Vector | Broadcast/Multicast (224.0.0.9) |
| **EIGRP** | IP Protocol 88 | Advanced Distance Vector | Multicast (224.0.0.10) |
| **OSPF** | IP Protocol 89 | Link State | Multicast (224.0.0.5, 224.0.0.6) |
| **BGP** | TCP 179 | Path Vector | TCP connection |
| **IS-IS** | Layer 2 (CLNS) | Link State | Directly over data link |

### Port Range Categories:

| Range | Category | Usage | Assignment |
|-------|----------|-------|------------|
| **0 - 1023** | Well-Known Ports | System/common services | IANA assigned |
| **1024 - 49151** | Registered Ports | User/vendor applications | IANA registered |
| **49152 - 65535** | Dynamic/Private Ports | Temporary/ephemeral | Not assigned |

---

## Cable Types Comparison

Physical layer transmission media comparison:

| Cable Type | Max Distance | Bandwidth | Speed | Interference Resistance | Cost | Best Use Case | Connector Types |
|------------|-------------|-----------|-------|----------------------|------|---------------|-----------------|
| **UTP (Cat5)** | 100m | 100 MHz | Up to 100 Mbps | Low | $ | Basic Ethernet | RJ-45 |
| **UTP (Cat5e)** | 100m | 100 MHz | Up to 1 Gbps | Low | $ | Gigabit Ethernet | RJ-45 |
| **UTP (Cat6)** | 100m (55m at 10G) | 250 MHz | Up to 10 Gbps | Medium | $$ | Modern networks | RJ-45 |
| **UTP (Cat6a)** | 100m | 500 MHz | Up to 10 Gbps | Medium | $$ | 10G networks | RJ-45 |
| **UTP (Cat7)** | 100m | 600 MHz | Up to 10 Gbps | High | $$$ | High-end installations | GG45/TERA |
| **STP** | 100m | Similar to UTP | Similar to UTP | High | $$ | Industrial environments | RJ-45 |
| **Coaxial** | 500m | Up to 3 GHz | Up to 10 Gbps | Medium | $$ | Cable TV, legacy networks | BNC, F-type |
| **Single-mode Fiber** | 100+ km | Very High | Up to 100+ Gbps | Very High | $$$$ | Long distance, WAN | LC, SC, ST |
| **Multi-mode Fiber** | 2 km | Very High | Up to 100 Gbps | Very High | $$$ | Campus backbone, datacenter | LC, SC, ST |

### Cable Wiring Standards:

| Standard | Pin Configuration | Use Case | Cable Type Required |
|----------|------------------|----------|-------------------|
| **T568A Straight** | Same on both ends (T568A) | PC to Switch/Hub | Straight-through |
| **T568B Straight** | Same on both ends (T568B) | PC to Switch, Switch to Router | Straight-through |
| **Crossover** | T568A one end, T568B other end | PC to PC, Switch to Switch, Router to Router | Crossover |
| **Rollover** | Pin 1→8, 2→7, 3→6, etc. | PC to Router/Switch Console | Rollover (Console cable) |

### Straight-Through vs Crossover:

| Connection Type | Device 1 | Device 2 | Cable Required | Reason |
|----------------|----------|----------|----------------|---------|
| **Straight-Through** | PC | Switch | Straight | Different device types |
| **Straight-Through** | Switch | Router | Straight | Different device types |
| **Straight-Through** | Router | Hub | Straight | Different device types |
| **Crossover** | PC | PC | Crossover | Same device type |
| **Crossover** | Switch | Switch | Crossover | Same device type |
| **Crossover** | Router | Router | Crossover | Same device type |
| **Crossover** | PC | Router | Crossover | Special case (both are DTE) |
| **Straight-Through** | Switch | Hub | Straight | Special case (different collision handling) |

**Note:** Modern devices with Auto-MDIX automatically detect cable type and adjust accordingly.

---

## VLAN Configuration Comparison

Comparison of VLAN implementation methods:

### VLAN Overview:

| VLAN Aspect | Description | Range/Details |
|-------------|-------------|---------------|
| **VLAN ID Range** | 12-bit identifier | 1 - 4094 (0 and 4095 reserved) |
| **Default VLAN** | Untagged traffic VLAN | VLAN 1 (cannot be deleted) |
| **Normal VLANs** | Standard configuration range | 1 - 1005 |
| **Extended VLANs** | Additional range (requires VTP transparent) | 1006 - 4094 |
| **Reserved VLANs** | Cannot be used | 0, 4095 |
| **Native VLAN** | Untagged traffic on trunk | Default: VLAN 1 (should be changed) |

### Port Types Comparison:

| Port Type | Tag Behavior | VLANs Carried | Configuration | Use Case |
|-----------|-------------|---------------|---------------|----------|
| **Access** | Untagged | Single VLAN | `switchport access vlan X` | End devices (PCs, printers) |
| **Trunk** | Tagged (802.1Q) | Multiple VLANs | `switchport mode trunk` | Switch-to-switch, switch-to-router |
| **Hybrid** | Tagged + Untagged | Multiple (mixed) | Vendor-specific | Mixed environments (not recommended) |
| **Voice VLAN** | Tagged voice + untagged data | 2 VLANs (data + voice) | `switchport voice vlan X` | IP phones |

### Inter-VLAN Routing Methods:

| Method | Device | Scalability | Performance | Cost | Configuration Complexity | Best For |
|--------|--------|-------------|-------------|------|------------------------|----------|
| **Router-on-a-Stick** | Router + L2 Switch | Low (single link bottleneck) | Low-Medium | Low | Medium | Small networks, budget constraints |
| **Layer 3 Switch (SVI)** | L3 Switch | High | High | High | Medium-High | Medium to large networks |
| **Separate Router Interfaces** | Router + L2 Switch | Medium | Medium | Medium | Low | Multiple physical connections available |
| **External Router + Trunk** | Router + L2 Switch | Low | Low | Low | Medium | Legacy or temporary setups |

### Router-on-a-Stick vs Layer 3 Switch:

| Feature | Router-on-a-Stick | Layer 3 Switch (SVI) |
|---------|------------------|---------------------|
| **Device Required** | Router + Layer 2 Switch | Layer 3 Switch |
| **Sub-interfaces** | Yes (one per VLAN) | No (uses SVIs) |
| **Physical Interface** | 1 trunk link (bottleneck) | Internal backplane |
| **Performance** | Limited by single link | Wire-speed switching |
| **Latency** | Higher | Lower |
| **Cost** | Lower | Higher |
| **Scalability** | Limited | Excellent |
| **Configuration** | More complex | Moderate |
| **VLAN Limit** | Limited by router capacity | Hundreds of VLANs |
| **Best Use** | Small networks, < 10 VLANs | Enterprise, many VLANs |

### VLAN Trunking Protocols:

| Protocol | Standard | Tagging Method | Native VLAN Support | Max VLANs |
|----------|----------|----------------|-------------------|-----------|
| **802.1Q** | IEEE (Open) | Tag insertion | Yes | 4094 |
| **ISL** | Cisco (Proprietary) | Frame encapsulation | No | 4094 |

**Note:** ISL is deprecated; 802.1Q is the industry standard.

---

## ACL Types Comparison

Comprehensive Access Control List comparison:

### Standard vs Extended ACLs:

| Feature | Standard ACL | Extended ACL |
|---------|-------------|--------------|
| **Number Range** | 1-99, 1300-1999 | 100-199, 2000-2699 |
| **Filters Based On** | Source IP only | Source IP, Destination IP, Protocol, Port |
| **Granularity** | Low | High |
| **Placement** | Close to destination | Close to source |
| **Processing** | Faster (fewer checks) | Slower (more checks) |
| **Flexibility** | Low | High |
| **Use Case** | Simple filtering | Complex filtering scenarios |
| **Example** | Block all traffic from 192.168.1.0/24 | Block HTTP from 192.168.1.2 to 10.0.0.5 |

**Document Version:** 1.0  
**Course:** ENCS4130 - Computer Networks Laboratory  
**Institution:** Birzeit University  
**Last Updated:** February 2025

---

*This guide is intended for educational purposes as part of the Computer Networks Laboratory course.*
