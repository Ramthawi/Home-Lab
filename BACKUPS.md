

```markdown
# Standard Operating Procedures (SOP): Network Configuration Backups

## Project Overview

This document outlines the standard operating procedures (SOP) for performing regular configuration backups across the multi-vendor network environment. Establishing a robust recovery point objective (RPO) ensures rapid network restoration in the event of hardware failure, catastrophic corruption, or configuration errors.

---

## 1. Backup Strategy Matrix

| Platform / Device | Backup Type | Storage Target | Frequency | Retention Policy | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **UniFi UDM-Pro** | Automated Cloud / Local `.unifi` | UniFi OS Cloud Portal / Secure Local NAS | Weekly (Automated) | Last 5 Versions | Gateway, Firewall, & Controller Recovery |
| **Cisco Catalyst Switch** | Manual Text-Based `.cfg` / TFTP | Local Secure Git Repository / TFTP Server | Post-Configuration Change | Version Controlled | IOS Running-Config Recovery |

---

## 2. UniFi Gateway (UDM-Pro) Backup Procedure

The UniFi Dream Machine Pro automatically backs up both system configuration and controller data, but manual offline backups should always be performed before major firmware upgrades.

### Automated Backups (Cloud Verification)

1. Log into your UniFi Portal (`unifi.ui.com`).
2. Navigate to **System Settings** -> **Backups**.
3. Verify that **Auto-Backup** is toggled **ON**.
4. Set the schedule to **Weekly** and retention to **Data Only** (or set a limit to conserve storage).

### Manual Backup (Pre-Upgrade Baseline)

1. Navigate to your UDM-Pro console dashboard.
2. Go to **Settings** -> **System** -> **Backups**.
3. Click **Download** to export a local `.unifi` backup file.
4. Save this file securely in your local offline storage directory following the naming convention:
   `udm_pro_backup_YYYYMMDD_v[firmware_version].unifi`

---

## 3. Cisco Switch Configuration Backup Procedure

Cisco devices maintain configurations in volatile RAM (`running-config`). Changes must be committed to non-volatile RAM (`startup-config`) and exported to ensure they survive a reboot or device swap.

### Step 1: Commit Changes to NVRAM

Always save your active session changes before exporting the configuration.

```text
Switch# copy running-config startup-config
Destination filename [startup-config]? [Press Enter]
Building configuration...
[OK]

```

### Step 2: Method A - Secure Text-Based Backup (Recommended for Git)

Capturing the running configuration as plain text allows you to track configuration changes over time using Git version control.

1. Establish an SSH connection to the Cisco Switch and start terminal logging in your client (e.g., PuTTY, Terminal).
2. Execute the following command to remove output pagination:
```text
Switch# terminal length 0

```


3. Display the entire active configuration:
```text
Switch# show running-config

```


4. Copy the output text, sanitize any sensitive credential hashes, and save it locally as:
`cisco_switch_config_YYYYMMDD.cfg`
5. Restore standard output pagination:
```text
Switch# terminal length 24

```



### Step 3: Method B - Automated Network Backup via TFTP

For rapid bare-metal restores, copy the configuration file directly from the switch to an active TFTP server on your network.

```text
Switch# copy running-config tftp:
Address or name of remote host []? 10.0.0.100
Destination filename [switch-confg]? cisco_switch_config_YYYYMMDD.cfg
!!
[OK - 4829 bytes]

```

---

## 4. Disaster Recovery (DR) Verification

To ensure backups are functional and restorable, audit the files monthly using the following checklist:

* [ ] **Integrity Verification:** Check that the `.unifi` and `.cfg` files are not 0 KB in size.
* [ ] **Readability Check:** Open the Cisco `.cfg` file in a standard text editor to confirm command syntax is legible and intact.
* [ ] **Hardening Check:** Verify that local secret hashes in the `.cfg` file remain securely encrypted (`enable secret 5...` or `enable secret 9...`).

```

```
