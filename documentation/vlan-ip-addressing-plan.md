# VLAN & IP Addressing Plan

## Project

Enterprise Office VLAN Network — Cisco Packet Tracer


## 1. Network Overview

The office network is divided into multiple VLANs to separate departments, servers, guest users, network management, and IP phones.

Each VLAN represents a separate Layer 2 broadcast domain and uses a separate IP subnet.

Inter-VLAN communication is handled by the Cisco 2911 router using Router-on-a-Stick.

---

## 2. VLAN Plan

| VLAN ID | VLAN Name | Purpose | Network | Default Gateway |
|--------:|-----------|---------|---------|-----------------|
| 10 | ADMIN | Administration | 192.168.10.0/24 | 192.168.10.1 |
| 20 | HR | Human Resources | 192.168.20.0/24 | 192.168.20.1 |
| 30 | IT | IT Department | 192.168.30.0/24 | 192.168.30.1 |
| 40 | FINANCE | Finance Department | 192.168.40.0/24 | 192.168.40.1 |
| 50 | SALES | Sales Department | 192.168.50.0/24 | 192.168.50.1 |
| 60 | SUPPORT | Customer Support | 192.168.60.0/24 | 192.168.60.1 |
| 70 | SERVERS | Server Farm | 192.168.70.0/24 | 192.168.70.1 |
| 80 | GUEST | Guest Network | 192.168.80.0/24 | 192.168.80.1 |
| 90 | MANAGEMENT | Network Device Management | 192.168.90.0/24 | 192.168.90.1 |
| 110 | VOICE | IP Phones / VoIP | 192.168.110.0/24 | 192.168.110.1 |
| 999 | NATIVE-BLACKHOLE | Unused Native VLAN | No user subnet | None |

---

## 3. Subnet Details

All production VLANs use a /24 subnet.

Subnet Mask:

```text
255.255.255.0