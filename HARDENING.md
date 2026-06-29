

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

! Enable global password encryption service
Switch(config)# service password-encryption

! Set a securely hashed Enable Secret password for privileged exec mode
Switch(config)# enable secret [REDACTED_SECURE_HASH]

! Select all unused ports via interface range
Switch(config)# interface range gigabitEthernet 1/0/4 - 48
Switch(config-if-range)# shutdown
Switch(config-if-range)# description ADMINISTRATIVELY DISABLED / UNUSED PORT
Switch(config-if-range)# exit

Switch(config)# interface gigabitEthernet 1/0/3
! Disable dynamic trunk negotiation
Switch(config-if)# switchport nonegotiate
Switch(config-if)# exit

Switch(config)# banner motd ^C
======================================================================
     UNAUTHORIZED ACCESS TO THIS RESOURCE IS STRICTLY PROHIBITED
  All activities are logged. Disconnect immediately if unauthorized.
======================================================================
^C
Switch(config)# end
Switch# write memory

