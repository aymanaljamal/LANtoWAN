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

**Document Version:** 1.0  
**Course:** ENCS4130 - Computer Networks Laboratory  
**Institution:** Birzeit University  
**Last Updated:** February 2025

---

*This guide is intended for educational purposes as part of the Computer Networks Laboratory course.*
