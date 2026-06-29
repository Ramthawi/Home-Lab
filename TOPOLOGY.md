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


3. Interface & Port AllocationsTo maintain structural organization, specific ports are designated for infrastructure uplinks, core production routing, and isolated lab access.DevicePhysical PortConnected ToLink TypeAssigned VLAN / ProfileUniFi UDM-ProWAN 1ISP ModemRouted LinkPublic IP / DHCPUniFi UDM-ProLAN Port 1UniFi Switch Port 1Downstream LinkLAN (Default)UniFi 24 SwitchPort 2Cisco Switch Gi1/0/1802.1Q TrunkNative: Default (1) / Tagged: 10Cisco 48 SwitchGi1/0/1UniFi Switch Port 2802.1Q TrunkNative: Default (1) / Tagged: 10Cisco 48 SwitchGi1/0/2Cisco Secondary RouterAccess LinkProduction LAN (VLAN 1)Cisco 48 SwitchGi1/0/3Homelab Test PCAccess Link


4. Architectural Design Logic
Trunking Architecture: The link between UniFi Port 2 and Cisco Port Gi1/0/1 uses 802.1Q encapsulation. This ensures that while standard production traffic passes through untagged, any packet originating from Cisco Port Gi1/0/3 is injected with a 4-byte VLAN 10 tag header, keeping it digitally segregated across the entire physical wire.

Management Boundary: The Cisco Switch management layer resides entirely on VLAN 1, allowing the primary administrator workspace to safely configure the distribution tier without exposing management consoles to the isolated testing environment.


---

## How to make the diagram for this:
1. Open [Draw.io](https://app.diagrams.net/).
2. Create three stacked rectangular layers. Label the top one **UniFi UDM-Pro**, the middle one **UniFi 24-Port Switch**, and the bottom one **Cisco 48-Port Switch**.
3. Draw a line from UniFi Switch (Port 2) to Cisco Switch (Port 1/0/1). Label that line text **"802.1Q Trunk (VLAN 1 & 10)"**.
4. Draw a device off Cisco Port 1/0/2 labeled **"Cisco Router"** and another device off Cisco Port 1/0/3 labeled **"Lab Device (VLAN 10)"**.
5. Export it as a PNG named `network-topology.png` and upload it into your main GitHub repository along with this file.

Once you have this map down, it frames your entire repository beautifully! Ready to move on to **Number 2: The Cisco Device Hardening Guide**?
