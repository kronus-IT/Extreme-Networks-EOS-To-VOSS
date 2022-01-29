# Extreme-Networks-EOS-To-VOSS
Extreme Networks converts VLAN and Interface configuration from EOS or Spreadsheet to VOSS

Have created my first Python script! The project gave me a real world example to practice.

Probably is the most terribly written program ever (still working on it), but it does function. There isn’t much exception handling, so if you use it and hit an issue let me know.

Will need to learn how to use Github, so I can properly load it there with new revisions.

It basically takes an EOS backup file, or an Excel configuration template file and produces all the VLAN / Interface configuration into VOSS. Equally it will take the EOS config and create a spreadsheet with all the configuration details like IP address, VLAN ID, VRRP VIP, Helpers etc.

Accompanying the python script file is an example EOS backup file called ‘eos_full_config.txt’, and the VOSS Excel configuration template ‘Config_Temp_Ver07’. At this time the script only supports the ‘Interface’ tab in the Excel file, but I do have another python script that creates the generic configs from the ‘Fabric’ tab that fully creates the VOSS ready to roll. This will be added in later additions.

This is configured in Python 3, and will need to do a PIP install for openpyxl and inquirer.

Here is an example of what the ‘interface’ tab in the Excel configuration template file looks like:

The Excel configuration template might be useful to someone on its own, as is something I generally work with customers to fill out on a new deployment.

The EOS backup file and the Excel (.xlsx) needs to be in the same location as the python script file.

A series of questions will be asked when the script runs where you choose:

•	To use EOS backup file, or
•	To use the EXCEL Config Template.
•	If you would like to create a spreadsheet with all the config details (based on EOS backup).
•	If you want to include the VOSS VLAN create commands or just the Interface commands
•	What prefix you want to use for the i-sid

This program will extrapolate the following from an EOS backup file:

set vlan name 64 "Power House GUEST LAN"
interface vlan.0.64
  ip address 10.119.64.3 255.255.255.0 primary
  vrrp create 1 v2-IPv4
  vrrp address 1 110.119.64.1 
  vrrp priority 1 200
  vrrp accept-mode 1
  vrrp enable 1
  ip helper-address 10.119.0.140 
  ip helper-address 10.119.0.200 
  no ip redirects
  exit

And convert to something like the below.

•	Even VLANs are given a VRID of 112 and priority of 200, odd VLANs are given a VRID of 111 and a priority of 150.
•	If the VLAN name is not present the name will be VLAN_<VLAN ID>
•	All interfaces are OSPF enabled and passive.
•	VRRP version 3 is used.

vlan create 64 name Power House GUEST LAN type port-mstprstp 0
vlan i-sid 64 12110064
interface vlan 64
ip address 10.119.255.10 255.255.255.0
ip vrrp version 3
ip vrrp address 2 112 
ip vrrp 112 backup-master enable
ip vrrp 112 enable
ip vrrp 112 priority 200
ip dhcp-relay
ip dhcp-relay mode dhcp
ip ospf area 0.0.0.0
ip ospf network passive
ip ospf enable
ip igmp snooping
exit
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.140
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.140 enable
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.140 mode bootp_dhcp
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.200 
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.200 enable
ip dhcp-relay fwd-path 10.119.255.10 10.119.0.200 mode bootp_dhcp
exit

If anyone try’s it, open to any criticism, suggestions, problems with running it etc

Work will continue to improve it, tidy it up, and will later include conversation of EXOS and CISCO IOS config.
