

```markdown
# Cisco IOS Device Hardening & Security Specification

## Project Overview
This document outlines the security baseline configurations applied to the Cisco Catalyst distribution switch. In an enterprise environment, default configurations leave management planes vulnerable. The following steps were implemented to secure the device console, encrypt local credentials, restrict access methods, and mitigate layer-2 vulnerabilities.

---

## 1. Management Plane Security

### Disabling Insecure Protocols & Forcing SSHv2
By default, factory configurations may allow unencrypted Telnet access, which transmits credentials in cleartext over the wire. Management access has been strictly upgraded to SSHv2 with a cryptographic RSA key pair.

```text
Switch# configure terminal
! Configure local domain name required for cryptographic keys
Switch(config)# ip domain-name lab.example.local

! Generate RSA Key Pair (1024-bit minimum for SSHv2)
Switch(config)# crypto key generate rsa
     [Choose 1024 or 2048 bits when prompted]

! Enforce SSH version 2 and disable Telnet on VTY lines
Switch(config)# line vty 0 15
Switch(config-line)# transport input ssh
Switch(config-line)# login local
Switch(config-line)# exit

```

### Credential Encryption & Privileged Access

Local execution passwords must be cryptographically hashed within the running configuration to prevent shoulder-surfing or configuration-leak exposures.

```text
! Enable global password encryption service
Switch(config)# service password-encryption

! Set a securely hashed Enable Secret password for privileged exec mode
Switch(config)# enable secret [REDACTED_SECURE_HASH]

```

---

## 2. Layer 2 & Interface Hardening

### Disabling Unused Ports (The "Black Hole" Concept)

Unused physical ports left active present an unauthorized physical access risk. All unallocated interfaces on the 48-port switch have been administratively disabled.

```text
! Select all unused ports via interface range
Switch(config)# interface range gigabitEthernet 1/0/4 - 48
Switch(config-if-range)# shutdown
Switch(config-if-range)# description ADMINISTRATIVELY DISABLED / UNUSED PORT
Switch(config-if-range)# exit

```

### Access Port Security Boundary

For active ports assigned to endpoints (such as Port `Gi1/0/3`), Dynamic Trunking Protocol (DTP) must be disabled to prevent VLAN hopping attacks, where a rogue device attempts to spoof a switch to negotiate an unauthorized trunk link.

```text
Switch(config)# interface gigabitEthernet 1/0/3
! Disable dynamic trunk negotiation
Switch(config-if)# switchport nonegotiate
Switch(config-if)# exit

```

---

## 3. Operational Banners

Configured a legal notification banner visible during login attempts to explicitly deny unauthorized access, satisfying standard enterprise compliance requirements.

```text
Switch(config)# banner motd ^C
======================================================================
     UNAUTHORIZED ACCESS TO THIS RESOURCE IS STRICTLY PROHIBITED
  All activities are logged. Disconnect immediately if unauthorized.
======================================================================
^C
Switch(config)# end
Switch# write memory

```

---

## 4. Verification & Auditing

To verify the hardening status of the switch plane, the following operational commands are used to audit active security settings:

* `show ip ssh` -> Confirmed SSH v2 is active and running.
* `show interfaces status` -> Verified ports 4 through 48 show `disabled` under the operational status column.

```

```
