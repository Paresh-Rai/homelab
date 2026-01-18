# Lab 05 - Backup Server for backup configurations

---

## Objective/Overview

Set up a backup solution for my home lab to backup important configurations:
- Proxmox Virtual machine backups
- Network device configurations (Cisco switch and FortiGate firewall)

To make the document more simple, i have not included full configurations for the NAS as this was already setup before the homelab, and was already in use for other personal tasks like storing media files.

---

## Components Used

- Proxmox
- NAS (Ubuntu Server)
- Ubuntu VM (Ubuntu Server)
- Cisco Switch
- Fortinet Firewall

---

### Topology

![Backup solution Topology](images/Lab-05-Topologoy.png)

---

### NAS Server setup 

- Deployed Ubuntu Server on Dell OptiPlex 7090 SFF
- Configured RAID1 on two hdds using mdadm
- Created the directory /mnt/storage/backup for centralized backups
- Installed and configured NFS
- Exported backup directories via /etc/exports

![](images/.png)

---

### Proxmox 

- Setup new storage on proxmox, pointing to the nas
- Setup a backup job on proxmox

![Storage setup](images/Proxmox-Storage-setup.png)
![Backup job](images/Proxmox-Backup-setup.png)

---

### Ubuntu VM

- Installed nfs-common
- Installed python3-pip and venv for virtual environments
![Installtions on Ubuntu](images/Ubuntu-SS-01-nfs-and-python.png)
- Setup ssh keys using ssh-keygen
![ssh keygen](images/Ubuntu-SS-05-sshkeygen-rsa.png)
![ssh files](images/Ubuntu-SS-06-.ssh.png)
- Created a virtual environment using venv and installed netmiko
![venv](images/Ubuntu-SS-07-venv-setup.png)
- Created a backup script using python to automate backups
![script](images/Python-01.png)
![script](images/Python-02.png)
- Created directory for the NAS mount
![directory](images/Ubuntu-SS-02-directory-for-nfs.png)
- Configured automatic mounting of the NAS using /etc/fstab
![images](images/Ubuntu-SS-03-etc-fstab.png)

---

### FortiGate Firewall & Cisco 3750 Switch

- Configured key-based authentcation on the FortiGate and Switch

![Key authentication setup](images/Sw-ss-01-ssh.png)
![Key authentication setup](images/fgfw-ss-01-ssh.png)


---

### Troubleshooting/Notes

- Cisco 3750 only supported legacy key exchange algorithms and ciphers causing ssh connections to fail
- Created a custom ssh client configuration as a workaround
![ssh config](images/Ubuntu-SS-04-sshconfig.png)
- Whilst ssh key based authentication worked when using ssh directly from the terminal, the python script using netmiko did not
- As a workaround used the input() function and getpass() module for ssh authentication
- Username and password are only requested during script execution and not stored in script
- Proxmox and Ubuntu VM were getting access denied when trying to reach the NAS
- Used tcpdump on NAS to produce a pcap
![tcpdump](images/NAS-tcpdump.png)
- Open the pcap on Wireshark and saw that proxmox and ubuntu vm were being nated to the fgfw ip
![Wireshark](images/Wireshark.png)
- Added fgfw as a secondary client on the /etc/exports as a temporary workaround which solved the issue
![etc-exports](images/NAS-SS-01-etc-exports.png)
