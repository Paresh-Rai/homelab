
# Lab 01 
----------------------------------------------------------------------------------------------------------	

Setup a basic lab including the following components:
- Proxmox running on mini pc
- Server 2025 VM
- Windows 11 client VM
- Fortigate 60F firewall

I will be building upon this lab in the future e.g Intergating a Switch, AP to practice some network related techonlogies.
----------------------------------------------------------------------------------------------------------	

Topology:
- ![Topology](../../images/lab01/topology.png).
----------------------------------------------------------------------------------------------------------	

		Proxmox setup:
- created a new virtual bridge on proxmox which in this case was vmbr10.
![Proxmox vmbr10 configuration](../../images/lab01/bridge_setup.png).
- SSH, into the proxmox, created backup of /etc/networtk/interfaces using 'cp' in case needed to rollback.
![creating a backup](../../images/lab01/network-interface-backup.png).
- bind vmbr10 to the ethernet interface, made it vlan aware, add vlan 10,20,30  and applied changes.
----------------------------------------------------------------------------------------------------------	
	Windows Server 2025 setup:
- Changed computer name to Got-SRV-01 and rebooted
- configured the network settings with the following:
![Proxmox vmbr10 configuration](../../images/lab01/network-settings.png).
-Installed Active Direcotry Domain Services, DNS Server,DHCP, and rebooted.
-Promoted the server, and selected 'Add a new forest'
-Set the Root Domain name to GoT.local and rebooted.
![Proxmox vmbr10 configuration](../../images/lab01/server-settings.png).
-Launched Active Directory Users and Computers
-Under GoT.local, right cliked new OU and named it as Starks
-Inside starks OU, cliked on the create user icon and creatd a user called Ned Stark.
-Created two groups inside starks OU, Male, and Female.
![OU creation](../../images/lab01/OU-creation.png).

-Launched the DHCP management console, under actions clicked on authorize.
-Right cliked the IPv4 and created new scope.
-Defined the range of 10.10.10.100-10.10.10.200.
-in scope options made sure 003 router is 10.10.10.1, 006 is 10.10.10.10

- Right clicked the IPv4 and created two new scopes.
![DHCP Scopes](../../images/lab01/DHCP-Scope-creation.png)
![DHCP Scopes](../../images/lab01/DHCP-VLAN10-01.png)
![DHCP Scopes](../../images/lab01/DHCP-VLAN10-02.png)

![DHCP Scopes](../../images/lab01/DHCP-VLAN20-01.png)
![DHCP Scopes](../../images/lab01/DHCP-VLAN20-02.png)

-For both scopes, the starting and ending range is the same.
-However the scope options, the default gateway is different as they are in two different vlans.


----------------------------------------------------------------------------------------------------------	
	Windows 11 client setup:
- Configured network settings of windows 11 vm to be set to vmbr10, tagged it with vlan 20.
- Started the machine, and checked if it obtained the correct settings from dhcp using ipconfig /all
- tested connecivity with ping for both ip and dns.
- used ctrl + r and typed sysmd.cpl.
- on computer name tab, clicked change.
- selected domain, and entered GoT.local
- Prompoted for administrator crenditials, used from windows server 2025 vm.
- confirmed success and required reboot.
- Logged in using ned stark.
- confirmed vm is on the domain.
![Client obtained network information via DHCP](../../images/lab01/WM-CLIENT-VLAN20.png)

----------------------------------------------------------------------------------------------------------	
	Fortigate 60F setup:
-Configured 2 Vlans under port1 with the following parameters:

Vlan ID	| IP address | Subnetmask    | 
-------------------------------------|
10      | 10.10.10.1 | 255.255.255.0 |
-------------------------------------|
20      | 10.10.20.1 | 255.255.255.0 |
-------------------------------------|

- Configured admistrivate acces of ping, https, http, and ssh.
- Created firewall policy to allow 10.10.10.0/24 to reach internet via wan1 using the nat as the outgoing interface.
- Connected wan1 to the home router, it reterived ip information via dhcp,

![vlan-sub-interfaces](../../images/lab01/dhcp_relay.png)

