# Home-Lab


# Enterprise Network Isolation & Multi-Vendor Trunking (VLAN 10)

## Project Overview
This project demonstrates the design and implementation of a secure, isolated Homelab environment using a multi-vendor enterprise network topology. By leveraging Layer 3 routing, Virtual Local Area Networks (VLANs), and 802.1Q trunking, I successfully isolated development traffic from a production network while maintaining multi-vendor interoperability.

## Hardware Utilized
* **Gateway/Router:** Ubiquiti UniFi Dream Machine Pro (UDM-Pro)
* **Core Switch:** UniFi Managed Switch
* **Distribution Switch:** Cisco Catalyst 48-Port Managed Switch

## Network Architecture
* **Production LAN:** `192.168.1.0/24` (Native/Management traffic)
* **Homelab VLAN (VLAN 10):** `10.0.0.0/24` (Isolated development zone)
* **Routing:** Inter-VLAN routing handled via the upstream gateway with firewall rules configured to restrict cross-VLAN communication.

---

## Configuration Steps

### Step 1: Gateway & VLAN Creation (UniFi)
1. Created a new Virtual Network named **Homelab-Network**.
2. Assigned **VLAN ID 10**.
3. Configured the gateway IP to the `.1` host address of the `10.0.0.0/24` subnet.
4. Enabled DHCP Server to lease addresses dynamically within the pool.

### Step 2: 802.1Q Trunk Link Configuration (UniFi to Cisco)
To pass multiple networks over a single physical link, the connection between the switches was upgraded from an Access Port to a Trunk Port.
1. Navigated to UniFi Port Manager -> **Port 2** (Uplink to Cisco).
2. Maintained the Native Network on the Default VLAN to preserve management access.
3. Explicitly allowed **VLAN 10 (Homelab-Network)** as a tagged network on the interface.

### Step 3: IOS Trunk & Access Port Configuration (Cisco Switch)
Logged into the Cisco Switch CLI via SSH to configure the downstream trunk link and allocate user access ports.

```text
! Configure the 802.1Q uplink trunk port to UniFi Switch
Switch# configure terminal
Switch(config)# interface gigabitEthernet 1/0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

! Assign an access port to the isolated Homelab VLAN
Switch(config)# interface gigabitEthernet 1/0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# end
Switch# write memory
