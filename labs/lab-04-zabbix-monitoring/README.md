# Lab 04 – Zabbix Monitoring & Alerting

---

## Objective/Overview

Deploy a monitoring solution to monitor and alert for any issues in the homelab. For simplicitiy, I decided not to include the CA, NPS, AP, and use snmpv2 for this setup.

---

## Components Used

- Proxmox (Hypervisor)
- Ubuntu Server VM
- Zabbix 7.0
- Cisco 3750X Switch
- FortiGate Firewall
- Windows Server VM
- Linux Server VM
- External SMTP Relay (Brevo)

---

### Topology

![Zabbix Monitoring Topology](images/Zabbix-Topologoy.png)

---

### Zabbix Server Installation (Linux)

- Deployed an Ubuntu Server VM on Proxmox
- Added the official **Zabbix 7.0 repositories**
- Installed and configured required services:
  - Apache
  - MySQL
  - PHP
  - Zabbix Server and Frontend
- DBPassword has been blurred for security.
![Zabbix Setup](images/zabbix-vm-setup-01.png)
![Zabbix Setup](images/zabbix-vm-setup-02.png)
![Zabbix Setup](images/zabbix-vm-setup-03.png)
![Zabbix Setup](images/zabbix-vm-setup-04.png)

---

### Cisco 3750X Switch

- Configured SNMPv2
- Restricted SNMP access to the Zabbix server using a Standard 
ACL
- Community string has been blurred for security
![Cisco SW setup](images/SW-snmp-01.png)

---

### FortiGate Firewall 

- Configured SNMPv2
- Used the default SNMP events
- Community string has been blurred for security
![FGFW Setup](images/FGFW-01.png)
![FGFW Setup](images/FGFW-02.png) # blur the community_name

---

### Windows Server

- Downloaded Zabbix Agent, and set it up
![Windows Server Setup](images/WM-downloads.png)
- Forgot to take screenshots during exact set up process, however inlcuded screenshot below of the .conf file
![Windows Server Setup](images/WM-zabbix-agent-settings.png)

---

### Linux Server
- Installed Zabbix Agent, enabled and started it using systemctl, and added the sever ip and hostname on the .conf file

![Linux Server Setup](images/linux-vm-01.png)
![Linux Server Setup](images/linux-vm-02.png)

---

### Zabbix WebUI Configuration

- Added each device/vm on via Data collection > hosts > create host
![Zabbix UI Setup](images/zabbix-UI-01.png)
- Selected the matching template for each host
- For Fortigate and the Cisco Switch, they needed the matching community string via the Macros tab, example screenshot below
- Commntiy string has been blurred for security
![Zabbix UI Setup](images/zabbix-UI-02.png)
![Zabbix UI Setup](images/zabbix-UI-03.png)

---

## Alerting Configuration 

- Created a new media type via ALerts > Media types > Create edia type
- Created a account with Brevo which will be used as an external SMTP relay - For simpilicty, have not included steps in Brevo
![Alert setup](images/zabbix-alert-01.png)
- Created two new message templates, one for a problem e.g interface down, and one for the problem recover e.g Interface back up
![Alert setup](images/zabbix-alert-02.png)

- Configured the admin account to be used for Alerting via Users > Users
![Alert setup](images/zabbix-alert-03.png)
![Alert setup](images/zabbix-alert-04.png)
- Created an alert  via ALerts > actions > trigger actions
![ALert setup](images/zabbix-alert-05.png)
![Alert setup](images/zabbix-alert-06.png)
![Alert setup](images/zabbix-alert-07.png)

---

## Testing & Validation

- Stimulated failure event via shutdown an interface on the switch
- Received email for the problem and email when problem was resolved
![Email Test](images/zabbix-alert-08.PNG)
![Email Test](images/zabbix-alert-09.PNG)

---

## Notes/Next Steps

- Implement SNMP Traps for real time alerts
- Create custom dashboards e.g hosts with highest traffic

---
