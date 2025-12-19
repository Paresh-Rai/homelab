# Lab 02 – 802.1X Implementation 

---

## Overview

This lab builds upon **Lab 01**, where the initial components were set up using Proxmox and the FortiGate 60F firewall.  
To make the document easier to read, the steps involving initial setup (creating VMs, installing roles and features, setting up the Certificate Authority) have been omitted.  

**Lab Goal:** Implement 802.1X authentication.  

**Components used:**
- Certificate Authority Server VM
- Network Policy Server VM
- Cisco Switch
- Cisco Access Point
- Windows 11 Client VM  

**Additional Configurations:**
- Guest SSID with a captive portal
- Traffic shaping: max bandwidth 20 Mbps download and 5 Mbps upload

---

## Topology

![Lab 02 Topology](images/lab02-topology.png)

---

## Certificate Authority (CA) Server Setup

### Certificate Templates

Two templates were created:

1. **Computer Template**
    - Duplicate of the default Computer template
    - Compatibility set to highest supported by environment
    - Images:
        - ![Template Settings](images/lab02-CA-template.01.png)
        - ![Display Name](images/lab02-CA-display-name.02.png)
        - ![Subject Name Tab](images/lab02-CA-subject-name-tab-03.png)
        - ![Security Tab](images/lab02-CA-security-tab-04.png)

2. **RAS/IAS Server Template**
    - Duplicate of RAS/IAS Server template
    - Verified Application Policies include Server Authentication (OID 1.3.6.1.5.5.7.3.1)
    - Security configured to allow group read/enroll permissions
    - Images:
        - ![RAS Template 01](images/lab02-CA-RAS-01.png)
        - ![RAS Template 02](images/lab02-CA-RAS-02.png)
        - ![RAS Template 03](images/lab02-CA-RAS-03.png)
        - ![RAS Template 04](images/lab02-CA-RAS-04.png)
        - ![RAS Template 05](images/lab02-CA-RAS-05.png)

---

### GPO for Certificate Auto-Enrollment

- Edited **Default Domain Policy** to enable auto-enrollment
- Path: `Computer Configuration > Policies > Windows Settings > Security Settings > Public Key Policies > Certificate Services Client - Auto-Enrollment`
- Enabled the policy and checked necessary options
- Images:
    - ![GPO 01](images/lab02-GPO-01.png)
    - ![GPO 02](images/lab02-GPO-02.png)
    - ![GPO 03](images/lab02-GPO-03.png)

---

## Network Policy Server (NPS) Setup

1. **Verify Server Certificate**
    - Checked in `certlm` → Personal → Certificates
    - ![NPS Certificate](images/lab02-NPS-01.png)

2. **RADIUS Client Configuration**
    - Added the AP and switch as RADIUS clients
    - ![NPS RADIUS Client](images/lab02-NPS-02.png)

3. **Connection Request Policy**
    - Left as default

4. **Network Policy**
    - Configured policy rules for client authentication
    - Images:
        - ![NPS Policy 03](images/lab02-NPS-03.png)
        - ![NPS Policy 04](images/lab02-NPS-04.png)
        - ![NPS Policy 05](images/lab02-NPS-05.png)
        - ![NPS Policy 06](images/lab02-NPS-06.png)
        - ![NPS Policy 07](images/lab02-NPS-07.png)
        - ![NPS Policy 08](images/lab02-NPS-08.png)

---

## Cisco Access Point (AP) Configuration

- Created a new WLAN with **WPA2/Enterprise**
- Configured RADIUS server pointing to NPS
- VLAN ID initially set to 44 for dynamic VLAN assignment (later temporarily set to 25 due to client connectivity issues)
- Verified Windows 11 client obtained a certificate
- Troubleshooting:
    - Enabled **AAA Override** to allow dynamic VLAN assignment
    - Client successfully received VLAN 25 from NPS

**Images:**
- ![AP 01](images/lab02-AP-01.png)
- ![AP 02](images/lab02-AP-02.png)
- ![AP 03](images/lab02-AP-03.png)
- ![AP 04](images/lab02-AP-04.png) 

- ![AP 05](images/lab02-AP-05.png) 
- ![AP 06](images/lab02-AP-06.png) 
- ![AP 07](images/lab02-AP-07.png) 
- ![AP 08](images/lab02-AP-08.png) 

- ![AP 09](images/lab02-AP-09.png)  
- ![AP 10](images/lab02-AP-10.png) 
- ![AP 11](images/lab02-AP-11.png) 
- ![AP 12](images/lab02-AP-12.png) 

- ![AP 13](images/lab02-AP-13.png) 
- ![AP 14](images/lab02-AP-14.png) 

---

## Cisco Switch Configuration

- Created all required VLANs
- Configured interfaces:
  - Gi2/0/1, Gi2/0/5, Gi2/0/8, Gi2/0/13, VLAN55
- SSH configured on VTY lines for secure management

**Images:**
- ![Switch 01](images/lab02-SW-01.png)
- ![Switch 02](images/lab02-SW-02.png)
- ![Switch 03](images/lab02-SW-03.png)
- ![Switch 04](images/lab02-SW-04.png)

---

## Guest SSID Configuration

- WLAN Name: `Guest-lab`
- Enabled Guest Network and Captive Network Assistant
- VLAN ID: 30
- Traffic Shaping: 20 Mbps down and 5 Mbps UP
- Tested guest client connection and bandwidth
- configured Guest Splash Page.

**Images:**
- ![Guest 01](images/lab02-Guest-01.png)
- ![Guest 02](images/lab02-Guest-02.png)
- ![Guest 03](images/lab02-Guest-03.png)
- ![Guest 04](images/lab02-Guest-04.png)
- ![Guest 05](images/lab02-Guest-05.png)
---

## Guest Client Test

- Joined Guest-lab from an iPhone
- Clicked **Connect** o the captive portal
- Verified the client received an IP address via DHCP
- Performed a speed test to confirm taffic shapping was applied

**Images:**
- ![Guest 06](images/lab02-Guest-06.png)
- ![Guest 07](images/lab02-Guest-07.png)
- ![Guest 08](images/lab02-Guest-08.png)
- ![Guest 09](images/lab02-Guest-09.png)
- ![Guest 10](images/lab02-Guest-10.png)
- ![Guest 11](images/lab02-Guest-11.png)
---

## Notes

- Dynamic VLAN assignment via RADIUS was initially misconfigured and later resolved by enabling AAA Override on the access point.
- Future improvements may include:
  - Separate GPOs instead of Default Domain Policy
  - Additional NPS policy hardening
  - Wired 802.1X testing on switch ports
