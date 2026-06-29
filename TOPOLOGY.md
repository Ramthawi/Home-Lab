Ahmad-Khwaileh-HomeLab


# Network Topology Diagram & Design Specification

This document provides a comprehensive visual map and technical breakdown of the physical and logical layout connecting the Ubiquiti UniFi core environment to the Cisco Catalyst distribution layer.

## 1. Visual Network Topology Map

<img width="2562" height="1650" alt="network-topology" src="https://github.com/user-attachments/assets/0e6fd797-9201-4a60-8bbf-00327e703df1" />


---

## 2. Component Interconnection & Physical Layout

The physical cabling path dictates how frames travel up to the security gateway. The connections are structured as follows:

```text
  [ ISP Internet Source ]
            │ (WAN Port)
  ┌─────────────────────────┐
  │  UniFi UDM-Pro Gateway  │  <-- Handles Inter-VLAN Routing & Firewall
  └─────────────────────────┘
            │ (LAN SFP+ / RJ45 Uplink)
  ┌─────────────────────────┐
  │  UniFi 24-Port Switch   │  <-- Production Core Layer
  └─────────────────────────┘
            │ Port 2 (802.1Q Trunk Link)
            │
            │ Port GigabitEthernet1/0/1 (802.1Q Trunk Link)
  ┌─────────────────────────┐
  │  Cisco Catalyst 48-Port │  <-- Distribution Layer
  └─────────────────────────┘
     │                  │
     │ Port Gi1/0/2     │ Port Gi1/0/3
 [Cisco Router]     [Homelab Workstation]
 (Production)       (Isolated VLAN 10)


3. Interface & Port Allocations
To maintain structural organization, specific ports are designated for infrastructure uplinks, core production routing, and isolated lab access.

Device	Physical Port	Connected To	Link Type	Assigned VLAN / Profile
UniFi UDM-Pro	WAN 1	ISP Modem	Routed Link	Public IP / DHCP
UniFi UDM-Pro	LAN Port 1	UniFi Switch Port 1	Downstream Link	LAN (Default)
UniFi 24 Switch	Port 2	Cisco Switch Gi1/0/1	802.1Q Trunk	Native: Default (1) / Tagged: 10
Cisco 48 Switch	Gi1/0/1	UniFi Switch Port 2	802.1Q Trunk	Native: Default (1) / Tagged: 10
Cisco 48 Switch	Gi1/0/2	Cisco Secondary Router	Access Link	Production LAN (VLAN 1)
Cisco 48 Switch	Gi1/0/3	Homelab Test PC	Access Link	Homelab-Network (VLAN 10)



4. Architectural Design Logic
Trunking Architecture: The link between UniFi Port 2 and Cisco Port Gi1/0/1 uses 802.1Q encapsulation. This ensures that while standard production traffic passes through untagged, any packet originating from Cisco Port Gi1/0/3 is injected with a 4-byte VLAN 10 tag header, keeping it digitally segregated across the entire physical wire.

Management Boundary: The Cisco Switch management layer resides entirely on VLAN 1, allowing the primary administrator workspace to safely configure the distribution tier without exposing management consoles to the isolated testing environment.



