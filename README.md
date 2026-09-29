# Enterprise Office VLAN Network — Cisco Packet Tracer

A practical enterprise networking project built in Cisco Packet Tracer to demonstrate VLAN segmentation, trunking, inter-VLAN routing, network management, voice networking, Layer 2 security, EtherChannel, STP, and structured network troubleshooting.

---

## 1. Project Overview

This project simulates the LAN infrastructure of a small-to-medium-sized company, **MunnaTech Solutions Ltd.**

The network is designed around multiple departments, servers, IP phones, guest users, and network management devices. VLANs are used to separate different types of users and services into independent broadcast domains, while Layer 3 routing enables controlled communication between those networks.

The project focuses on both **configuration and verification**, following practices commonly used when building and troubleshooting a real enterprise LAN.

The entire network was designed and implemented using **Cisco Packet Tracer and Cisco IOS CLI**.

---

## 2. Business Scenario

MunnaTech Solutions Ltd. needs a structured and manageable office network with the following requirements:

* Separate departments using dedicated VLANs.
* Connect multiple switches using trunk links.
* Allow required VLANs to travel across trunk connections.
* Provide communication between VLANs through Layer 3 routing.
* Place business servers in a dedicated server VLAN.
* Separate IP phone traffic from normal user traffic.
* Provide a dedicated guest network.
* Use a separate management VLAN for network devices.
* Apply basic Layer 2 security to access ports.
* Use EtherChannel for selected switch-to-switch connections.
* Maintain loop prevention using Spanning Tree Protocol (STP).
* Provide a structured method for identifying and resolving network failures.

---

# 3. Network Architecture

```text
                           Internet
                              |
                             R1
                    Cisco 2911 Router
                              |
                       Router-on-a-Stick
                              |
                           Trunk
                              |
                             SW1
                      Distribution Switch
                       /              \
              EtherChannel          EtherChannel
                  /                      \
                SW2                      SW3
          Access Switch            Access Switch
          /    |    \              /    |     \
       ADMIN   HR    IT         FINANCE SALES SUPPORT
                          

                             |
                        Server VLAN
                             |
             --------------------------------
             |       |       |              |
            DHCP    DNS     WEB          FILE SERVER


              IP Phones → Voice VLAN 110
              Guests    → Guest VLAN 80
              Switches  → Management VLAN 90
```

### Network Design

The design uses a hierarchical approach:

```text
End Devices
     ↓
Access Switches
     ↓
Distribution Switch
     ↓
Layer 3 Routing
     ↓
Internet / Other Networks
```

The access switches connect users and end devices, while the distribution switch aggregates the access-layer connections and forwards traffic toward the router.

---

# 4. VLAN and IP Addressing Plan

| VLAN | Name             | Purpose                   | Network             | Default Gateway |
| ---: | ---------------- | ------------------------- | ------------------- | --------------- |
|   10 | ADMIN            | Administration            | 192.168.10.0/24     | 192.168.10.1    |
|   20 | HR               | Human Resources           | 192.168.20.0/24     | 192.168.20.1    |
|   30 | IT               | IT Department             | 192.168.30.0/24     | 192.168.30.1    |
|   40 | FINANCE          | Finance Department        | 192.168.40.0/24     | 192.168.40.1    |
|   50 | SALES            | Sales Department          | 192.168.50.0/24     | 192.168.50.1    |
|   60 | SUPPORT          | Customer Support          | 192.168.60.0/24     | 192.168.60.1    |
|   70 | SERVERS          | Server Network            | 192.168.70.0/24     | 192.168.70.1    |
|   80 | GUEST            | Guest Network             | 192.168.80.0/24     | 192.168.80.1    |
|   90 | MANAGEMENT       | Network Device Management | 192.168.90.0/24     | 192.168.90.1    |
|  110 | VOICE            | IP Telephony              | 192.168.110.0/24    | 192.168.110.1   |
|  999 | NATIVE-BLACKHOLE | Unused Native VLAN        | No end-user network | —               |

### VLAN 999

VLAN 999 is reserved as the native VLAN and is not assigned to normal end-user devices.

This provides a dedicated VLAN for unused/native traffic instead of using a production user VLAN as the native VLAN.

---

# 5. Department Segmentation

Each department is placed into its own VLAN.

For example:

```text
Administration
      ↓
   VLAN 10
      ↓
192.168.10.0/24
```

```text
HR
 ↓
VLAN 20
 ↓
192.168.20.0/24
```

```text
IT
 ↓
VLAN 30
 ↓
192.168.30.0/24
```

The same design is applied to Finance, Sales, Support, Servers, Guest, Management, and Voice.

This creates separate Layer 2 broadcast domains and makes the network easier to manage and troubleshoot.

---

# 6. Access Port Design

End-user devices are connected through access ports.

Example:

```text
ADMIN PC
   |
   | Access Port
   |
  SW2
   |
 VLAN 10
```

An access port normally carries traffic for a single data VLAN.

Example configuration:

```text
interface fa0/1
 switchport mode access
 switchport access vlan 10
```

This ensures that the connected device becomes a member of the intended department VLAN.

---

# 7. Trunk Design

Trunk links are used between network devices when multiple VLANs need to travel across the same physical connection.

Example:

```text
VLAN 10
VLAN 20
VLAN 30
VLAN 40
VLAN 50
VLAN 60
VLAN 70
VLAN 80
VLAN 110
      |
    TRUNK
      |
     SW1
```

Instead of creating a separate physical link for every VLAN, a trunk can carry multiple VLANs over one connection using IEEE 802.1Q tagging.

Only the required VLANs are permitted on trunk links.

Example:

```text
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40,50,60,70,80,110
```

---

# 8. Native VLAN

The project uses VLAN 999 as the native VLAN.

```text
Native VLAN
     ↓
    999
     ↓
NATIVE-BLACKHOLE
```

Example:

```text
switchport trunk native vlan 999
```

VLAN 999 is not used for normal users or application traffic.

Using a dedicated unused VLAN for native traffic helps avoid accidentally placing production devices into the native VLAN.

---

# 9. Inter-VLAN Routing

VLANs are separate Layer 2 broadcast domains, so devices in different VLANs cannot communicate directly at Layer 2.

The project uses **Router-on-a-Stick** to provide Layer 3 communication between VLANs.

The router uses subinterfaces connected through a trunk link to the switch.

Example:

```text
G0/0.10  → VLAN 10  → 192.168.10.1
G0/0.20  → VLAN 20  → 192.168.20.1
G0/0.30  → VLAN 30  → 192.168.30.1
G0/0.40  → VLAN 40  → 192.168.40.1
G0/0.50  → VLAN 50  → 192.168.50.1
G0/0.60  → VLAN 60  → 192.168.60.1
G0/0.70  → VLAN 70  → 192.168.70.1
G0/0.80  → VLAN 80  → 192.168.80.1
G0/0.110 → VLAN 110 → 192.168.110.1
```

Each subinterface acts as the default gateway for its corresponding VLAN.

---

# 10. Voice VLAN

IP phones use a dedicated Voice VLAN.

A typical access-port design is:

```text
        PC
         |
         |
     IP Phone
         |
         |
      Switch
```

The traffic is logically separated:

```text
PC       → Data VLAN
IP Phone → Voice VLAN 110
```

This allows a PC and IP phone to share the same physical switch port while remaining logically separated.

Example:

```text
switchport mode access
switchport access vlan 30
switchport voice vlan 110
```

Here, the PC belongs to VLAN 30 while the phone uses VLAN 110.

---

# 11. Server VLAN

Business servers are placed in a dedicated Server VLAN.

```text
VLAN 70 — SERVERS
        |
        |-- DHCP Server → 192.168.70.10
        |
        |-- DNS Server  → 192.168.70.20
        |
        |-- Web Server  → 192.168.70.30
        |
        |-- File Server → 192.168.70.40
```

A dedicated server VLAN makes server traffic easier to identify, manage, monitor, and control.

It also provides a clear boundary where additional security policies can later be applied.

---


# 13. EtherChannel

Selected switch-to-switch connections use EtherChannel.

Instead of treating multiple physical links independently, EtherChannel combines them into one logical link.

```text
        SW1
       /   \
      /     \
     /       \
   SW2       SW3
```

Multiple physical connections can operate as a single logical Port-Channel.

Benefits include:

* Increased available bandwidth.
* Link redundancy.
* Better utilization of multiple physical connections.
* Simplified STP operation because the bundled links appear as one logical connection.

Verification:

```text
show etherchannel summary
```

---

 
---

# 15. Layer 2 Security

Basic Layer 2 security is applied to appropriate access ports.

One example is switchport security.

```text
switchport mode access
switchport port-security
```

Depending on the port requirements, additional port-security options can be configured.

The purpose is to control which devices are allowed to use specific access ports and reduce unauthorized device connections.

---

# 16. Network Security Policy

VLANs provide segmentation, but VLANs alone are not a complete security solution.

Communication between VLANs occurs through Layer 3 routing, where additional controls such as ACLs can be applied.

Example business policy:

```text
ADMIN  → Servers      ALLOW
IT     → Servers      ALLOW
HR     → Servers      ALLOW

HR     → Finance      RESTRICT
Guest  → Internal     RESTRICT
Guest  → Servers      RESTRICT
Guest  → Management   RESTRICT
```

The purpose of these policies is to demonstrate how business requirements can be translated into network access rules.

---


# 18. Verification Commands

The following Cisco IOS commands are used throughout the project to verify configuration and troubleshoot problems.

### VLAN Verification

```text
show vlan brief
```

### Trunk Verification

```text
show interfaces trunk
```

### Interface Status

```text
show interfaces status
```

### IP Interface Status

```text
show ip interface brief
```

### Routing Table

```text
show ip route
```

### MAC Address Table

```text
show mac address-table
```

### EtherChannel

```text
show etherchannel summary
```

### STP

```text
show spanning-tree
```

 

### Configuration

```text
show running-config
```

These commands are used not only to confirm that the configuration exists, but also to identify where a connectivity problem is occurring.

---

# 19. Troubleshooting Methodology

The project includes intentional configuration failures to practice real-world troubleshooting.

Examples include:

* Incorrect access VLAN.
* Trunk configuration failure.
* Incorrect allowed VLAN list.
* Native VLAN mismatch.
* Shutdown router interface.
* Incorrect IP address.
* Incorrect subnet mask.
* Incorrect default gateway.

Troubleshooting follows a structured process rather than changing configurations randomly.

```text
Physical Connectivity
        ↓
Interface Status
        ↓
VLAN Assignment
        ↓
Access Port
        ↓
Trunk
        ↓
Allowed VLANs
        ↓
Native VLAN
        ↓
IP Address
        ↓
Subnet Mask
        ↓
Default Gateway
        ↓
Routing
        ↓
ACL / Security Policy
        ↓
Connectivity Test
```

This approach helps identify the problem layer before making configuration changes.

---

# 20. Validation and Testing

The completed network is tested using both connectivity tests and Cisco IOS verification commands.

| Test                           | Expected Result                |
| ------------------------------ | ------------------------------ |
| Same-VLAN communication        | Successful                     |
| PC to default gateway          | Successful                     |
| Admin to Server                | Allowed                        |
| IT to Server                   | Allowed                        |
| HR to Finance                  | Restricted according to policy |
| Switch management reachability | Successful                     |
| Voice VLAN operation           | Separate from data VLAN        |
| Trunk VLAN transport           | Verified                       |
| EtherChannel                   | Operational                    |
| STP                            | Verified                       |
| Server connectivity            | Successful                     |

Testing is performed after configuration changes to confirm that the network behaves according to the intended design.

---

# 21. Skills Demonstrated

This project demonstrates practical knowledge of:

### Switching

* VLAN creation and configuration
* VLAN segmentation
* Access ports
* Trunk ports
* 802.1Q
* Native VLAN
* Allowed VLANs
* MAC address learning
* EtherChannel
* STP

### Routing

* Inter-VLAN routing
* Router-on-a-Stick
* Router subinterfaces
* Default gateways
* IP addressing
* Static routing concepts
* Routing verification

### Enterprise Networking

* Department-based network segmentation
* Server network design
* Guest network design
* Management network design
* Voice VLAN
* Basic Layer 2 security
* DHCP
* ACL concepts
* Network documentation

### Troubleshooting

* Layer-by-layer troubleshooting
* VLAN troubleshooting
* Trunk troubleshooting
* IP configuration troubleshooting
* Gateway troubleshooting
* Routing verification
* Connectivity testing

---


# 23. Learning Outcome

The purpose of this project is not simply to demonstrate Cisco commands.

It demonstrates how individual networking concepts work together to build a structured enterprise LAN.

```text
End Users
    ↓
Access VLAN
    ↓
Access Switch
    ↓
Trunk
    ↓
Distribution Layer
    ↓
Inter-VLAN Routing
    ↓
Security Policy
    ↓
Servers / Other Networks
```

Through this project, I practiced designing, configuring, verifying, documenting, and troubleshooting a multi-VLAN Cisco network rather than working with isolated configuration examples.

---

# 24. Platform and Technologies

**Platform**

* Cisco Packet Tracer

**Network Devices**

* Cisco 2911 Router
* Cisco 2960 Switch

**Technologies**

* VLAN
* 802.1Q Trunking
* Router-on-a-Stick
* Inter-VLAN Routing
* Voice VLAN
* Server VLAN
* Guest VLAN
* EtherChannel
* STP
* Cisco IOS CLI

---

# 25. Project Status

**Status: Completed — Core network design, configuration, verification, and troubleshooting are complete, with additional enterprise features and security improvements planned for future iteration**

The project was completed as a practical enterprise switching and VLAN portfolio project in Cisco Packet Tracer.

The final implementation covers network segmentation, trunking, inter-VLAN routing, voice and management networks, server connectivity, Layer 2 security, EtherChannel, STP, verification, and structured troubleshooting.
