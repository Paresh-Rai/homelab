# Lab 04 – Zabbix 7.0 Enterprise Monitoring & Alerting

---

## Overview

This lab documents the deployment and configuration of **Zabbix 7.0** as the centralized monitoring and alerting platform for my homelab environment.

The Zabbix server was deployed on a newly built Linux VM, and configured to monitor **network, server, and security devices** using both SNMP and agent-based monitoring.

**Lab Goal:**  
Implement a monitoring solution with automated discovery, visual topology mapping, and email alerting.

---

## Components Used

- Proxmox (Hypervisor)
- Ubuntu Server (Zabbix Server)
- Zabbix 7.0
- Cisco 3750X Switch
- FortiGate Firewall
- Windows Server VM
- Linux Server VM
- External SMTP Relay (Brevo)

---

## Topology

![Zabbix Monitoring Topology](images/lab04-zabbix-topology.png)

---

## Zabbix Server Installation (Linux)

- Deployed an Ubuntu Server VM on Proxmox
- Added the official **Zabbix 7.0 repositories**
- Installed and configured required services:
  - Apache
  - MySQL
  - PHP
  - Zabbix Server and Frontend

### Database Configuration

- Created a dedicated MySQL database for Zabbix
- Configured database encoding as `utf8mb4_bin`

### Issue Encountered – Zabbix Server Not Running

- **Problem:** Zabbix Web UI reported *“Zabbix server is not running”*
- **Root Cause:** PHP configuration mismatch with the Zabbix server socket
- **Resolution:**
  - Updated `/etc/zabbix/zabbix_server.conf`
  - Aligned settings with `/etc/zabbix/apache.conf`
  - Verified service status using:
    ```bash
    ss -tulpn | grep 10051
    ```

---

## Device Integration

### Cisco 3750X Switch (Network Tier)

- Monitoring method: **SNMPv2c**
- Configured SNMP community string
- Restricted SNMP access to the Zabbix server using a Standard ACL
- Enabled SNMP traps for interface link status

### Windows & Linux Servers (Compute Tier)

- Installed Zabbix Agent (active/passive)
- Opened port **10050** on Windows Firewall and Linux iptables
- Verified successful communication with the Zabbix server

### FortiGate Firewall (Security Tier)

- Integrated using SNMP
- Monitored CPU usage, memory utilization, and VPN tunnel status

---

## Zabbix Frontend Configuration

- Manually added hosts using management IP addresses
- Applied official Zabbix templates:
  - **Cisco IOS by SNMP**
  - **Linux by Zabbix Agent**
  - **Windows by Zabbix Agent**

### Low-Level Discovery (LLD)

- Enabled LLD to automatically discover:
  - Switch interfaces
  - VLANs
  - Sensors
- Reduced the need for manual item creation and ongoing maintenance

---

## Topology Mapping

- Created a network map under **Monitoring → Maps**
- Linked devices to represent real network dependencies
- Configured trigger-based links:
  - ICMP and interface triggers change link color when failures occur
- Enabled at-a-glance visibility of network health

---

## Alerting Configuration (SMTP)

### SMTP Relay Setup

- Used **Brevo (formerly Sendinblue)** as an external SMTP relay
- Avoided ISP restrictions on outbound SMTP (Port 25)

**Configuration Details:**
- SMTP Server: `smtp-relay.sendinblue.com`
- Port: `587`
- Encryption: STARTTLS

### Zabbix Media Type

- Configured Email media type
- Defined message templates for:
  - Problem notifications
  - Recovery notifications

### User Media Configuration

- Added recipient email address to Admin user
- Enabled all severity levels for alert delivery

---

## Testing & Validation

- Verified SMTP relay using the Media Type test function
- Simulated failures to validate alerting:
  - Changed hostname on Cisco switch
  - Shut down switch interfaces
- Confirmed:
  - Triggers activated correctly
  - Dashboard reflected problem state
  - Email alerts and recovery notifications were delivered

---

## Notes / Troubleshooting

- **Issue:** *“No message defined in media type”*  
  **Resolution:** Added message templates for both problem and recovery events

- **Issue:** *“No media defined for user”*  
  **Resolution:** Assigned email address under **User → Media** and linked it to the SMTP media type

---

## Next Steps

- Secure Zabbix frontend with SSL
- Monitor additional services (HTTP, disk I/O, process monitoring)
- Add Telegram or Microsoft Teams alerting
- Monitor Proxmox host metrics and storage health

---
