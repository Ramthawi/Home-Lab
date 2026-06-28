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
