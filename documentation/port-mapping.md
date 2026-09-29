
# Port Mapping

## Project

Enterprise Office VLAN Network — Cisco Packet Tracer

---

## 1. Device Overview

| Device | Role |
|--------|------|
| R1 | Inter-VLAN Routing / Gateway |
| SW1 | Distribution Switch |
| SW2 | Access Switch — Admin, HR, IT |
| SW3 | Access Switch — Finance, Sales, Support, Servers |

---

# 2. SW1 Port Mapping

SW1 works mainly as the distribution switch.

| Port | Connection | Mode | Purpose |
|------|------------|------|---------|
| G0/1 | R1 G0/0 | Trunk | Router-on-a-Stick |
| Fa0/21 | SW2 Fa0/21 | EtherChannel | Trunk |
| Fa0/22 | SW2 Fa0/22 | EtherChannel | Trunk |
| Fa0/23 | SW3 Fa0/21 | EtherChannel | Trunk |
| Fa0/24 | SW3 Fa0/22 | EtherChannel | Trunk |

SW1 does not normally connect end-user PCs.

---

# 3. SW2 Port Mapping

SW2 provides access connectivity for Administration, HR and IT.

## Administration

| Port | Device | Data VLAN | Voice VLAN |
|------|--------|-----------|------------|
| Fa0/1 | Admin-PC1 + IP Phone | 10 | 110 |
| Fa0/2 | Admin-PC2 | 10 | — |

## HR

| Port | Device | Data VLAN | Voice VLAN |
|------|--------|-----------|------------|
| Fa0/3 | HR-PC1 + IP Phone | 20 | 110 |
| Fa0/4 | HR-PC2 | 20 | — |

## IT

| Port | Device | Data VLAN | Voice VLAN |
|------|--------|-----------|------------|
| Fa0/5 | IT-PC1 + IP Phone | 30 | 110 |
| Fa0/6 | IT-PC2 | 30 | — |

## Unused Ports

Unused ports should be placed into VLAN 999 and shut down where appropriate.

Example:

```text
Fa0/7 - Fa0/20