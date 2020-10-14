# Implement DHCPv4
	Task topology
 ![](/Labworks/Lab_03/Implement_DHCPv4/pics/scheme.jpg "Task topology")

	Addressing Table
|Device 		|Interface		|IP Address		|Subnet Mask 		|Default Gateway
|:--------------|:--------------|:--------------|:------------------|:------------------|
|R1				|G0/0/0			|10.0.0.1		|255.255.255.252	|N/A
|				|G0/0/1			|N/A			|N/A				|N/A
|				|G0/0/1			|N/A			|N/A				|N/A
|				|G0/0/1.100		|blank			|blank				|N/A
|				|G0/0/1.200		|blank			|blank				|N/A
|R2				|G0/0/0			|10.0.0.2		|255.255.255.252	|N/A
|				|G0/0/1			|blank			|blank				|N/A
|S1				|VLAN 200		|blank			|blank				|N/A
|S2				|VLAN 1			|blank			|blank				|N/A
|PC-A			|NIC			|DHCP			|DHCP				|DHCP
|PC-B 			|NIC			|DHCP			|DHCP				|DHCP


	VLAN Table

|VLAN 			|Name			|Interface Assigned
|:--------------|:--------------|:--------------|
|1				|N/A			|S2: F0/18
|100			|Clients		|S1: F0/6
|200			|Management 	|S1: VLAN 200
|999			|Parking_Lot	|S1: F0/1-4, F0/7-24, G0/1-2
|1000			|Native 		|N/A

	EVE-ng images

|Device	|Image
|:---	|:---
|Roter	|L3-ADVENTERPRISEK9-M-15.4-2T
|Switch	|i86bi_linux_l2-adveneterprisek9-ms.SSA.high_iron_20180510

	Table of compliance of ports of the task and virtual environment

|Device	|Task Interface	|Lab Interface	|
|:------|:--------------|:--------------|
|R1		|G0/0/0			|e0/0			|
|		|G0/0/1			|e0/1			|
|R2		|G0/0/0			|e0/0			|
|		|G0/0/1			|e0/1			|
|S1		|F0/5			|e0/1|
|		|F0/6			|e0/3|
|S2		|F0/5			|e1/1|
|		|F0/18			|e1/3|

	EVE Solvation topology  
 ![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic0_scheme.jpg "Solvation topology")



## Objectives
[Part 1: Build the Network and Configure Basic Device Settings](#part-1build-the-network-and-configure-basic-device-settings)  

[1.1 Establish an addressing scheme](#11-Establish-an-addressing-scheme)  
[1.2 Cable the network as shown in the topology](#12-Cable-the-network-as-shown-in-the-topology)  
[1.3 Configure basic settings for each router](#13-Configure-basic-settings-for-each-router)  
[1.4 Configure Inter-VLAN Routing on R1](#14-Configure-Inter-VLAN-Routing-on-R1)  
[1.5 Configure G0/0/1 on R2, then G0/0/0 and static routing for both routers](#15-configure-g001-on-r2-then-g000-and-static-routing-for-both-routers)  
[1.6 Configure basic settings for each switch.](#16-configure-basic-settings-for-each-switch)  
[1.7 Create VLANs on S1](#17-create-vlans-on-s1)  
[1.8 Assign VLANs to the correct switch interfaces](#18-assign-vlans-to-the-correct-switch-interfaces)  
[1.9: Manually configure S1’s interface F0/5 as an 802.1Q trunk](#19-manually-configure-s1s-interface-f05-as-an-8021q-trunk)  

[Part 2: Configure and verify two DHCPv4 Servers on R1](#part-2-configure-and-verify-two-dhcpv4-servers-on-r1)  

[2.1 Configure R1 with DHCPv4 pools for the two supported subnets. Only the DHCP Pool for subnet A is given below](#21-configure-r1-with-dhcpv4-pools-for-the-two-supported-subnets-only-the-dhcp-pool-for-subnet-a-is-given-below)  
[2.2 Save your configuration](#22-save-your-configuration)  
[2.3: Verify the DHCPv4 Server configuration](#23-verify-the-dhcpv4-server-configuration)
[2.4: Attempt to acquire an IP address from DHCP on PC-A](#24-attempt-to-acquire-an-ip-address-from-dhcp-on-pc-a)  

[Part 3: Configure and verify a DHCP Relay on R2](#3-configure-and-verify-a-dhcp-relay-on-r2)

----
### Part 1:	Build the Network and Configure Basic Device Settings  
In Part 1, we will set up the network topology and configure basic settings on the PC hosts and switches.

#### 1.1 Establish an addressing scheme
Subnet the network 192.168.1.0/24 to meet the following requirements:
a.	One subnet, “Subnet A”, supporting 58 hosts (the client VLAN at R1).  
**Subnet A:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet A	|58			|192.168.1.0	|192.168.1.1	|192.168.1.62	|192.168.1.63	|255.255.255.192

Record the first IP address in the Addressing Table for R1 G0/0/1.100. 
___192.168.1.1___

Record the second IP address in the Address Table for S1 VLAN 200  
___192.168.1.66___
and enter the associated default gateway.  
___192.168.1.65___


b.	One subnet, “Subnet B”, supporting 28 hosts (the management VLAN at R1).  
**Subnet B:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet B	|28			|192.168.1.64	|192.168.1.65	|192.168.1.94	|192.168.1.95	|255.255.255.224

Record the first IP address in the Addressing Table for R1 G0/0/1.200.  
___192.168.1.65___

Record the second IP address in the Address Table for S1 VLAN 1 and enter the associated default gateway.

c.	One subnet, “Subnet C”, supporting 12 hosts (the client network at R2).  
**Subnet C:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet C	|12			|192.168.1.96	|192.168.1.97	|192.168.1.110	|192.168.1.111	|255.255.255.240

Record the first IP address in the Addressing Table for R2 G0/0/1.  
___192.168.1.97___


#### 1.2 Cable the network as shown in the topology.
Attach the devices as shown in the topology diagram, and cable as necessary.


#### 1.3 Configure basic settings for each router.
a.	Assign a device name to the router.  
```
Router(config)# hostname R1
```
b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.
```
R1(config)# no ip domain-lookup
```
c.	Assign ___class___ as the privileged EXEC encrypted password.
```
R1(config)# enable secret class
```
d.	Assign ___cisco___ as the console password and enable login.
```
R1(config)# line con 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# logging synchronous
R1(config-line)# exit
R1(config)#
```
e.	Assign ___cisco___ as the VTY password and enable login.
```
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
```
f.	Encrypt the plaintext passwords.
```
R1(config)# service password-encryption
```
g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.  
```
R1(config)# banner motd #
Enter Text message. End with the character ‘#’.
Unauthorized access is strictly prohibited. #
```
h.	Save the running configuration to the startup configuration file.  
i.	Set the clock on the router to today’s time and date.  
```
R1#clock set 15:22:00 09 oct 2020
```

Note: Use the question mark (?) to help with the correct sequence of parameters needed to execute this command.

R1 basic settings  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_13_R1.jpg "R1 config")

R2 basic settings  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_13_R2.jpg "R2 config")


#### 1.4 Configure Inter-VLAN Routing on R1
a.	Activate interface G0/0/1 (e0/1) on the router.  
```
R1(config)# interface e0/1
R1(config)# no shutdown
```

b.	Configure sub-interfaces for each VLAN as required by the IP addressing table. All sub-interfaces use 802.1Q encapsulation and are assigned the first usable address from the IP address pool you have calculated. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.  

```
R1(config)# interface e0/1.100
R1(config-subif)#description Client_Network
R1(config-subif)#encapsulation dot1q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#exit
R1(config)# interface e0/1.200
R1(config-subif)#description Management
R1(config-subif)#encapsulation dot1q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#exit
R1(config)# interface e0/1.1000
R1(config-subif)#description Native
R1(config-subif)#encapsulation dot1q 1000 native
R1(config-subif)#end
```

c.	Verify the sub-interfaces are operational.
```
R1# show ip interface brief
```

![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_14_R1_c.jpg "R1 Verify the sub-interfaces")

#### 1.5 Configure G0/0/1 on R2, then G0/0/0 and static routing for both routers
a.	Configure G0/0/1 (e0/1) on R2 with the first IP address of Subnet C you calculated earlier.

```
R2#(config)#interface e0/1
R2#(config-if)#ip address 192.168.1.97 255.255.255.240
R2#(config-if)#no shutdown
R2#(config-if)#exit
```

![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_15_R2_a.jpg "R2")

b.	Configure interface G0/0/0 (e0/0) for each router based on the IP Addressing table above.  
On R2 we continue with next commands
```
R2#(config)#interface e0/0
R2#(config-if)#ip address 10.0.0.2 255.255.255.252
R2#(config-if)#no shutdown
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_15_R2_b.jpg "R2")

on R1 we will continue with
```
R1#conf t
R1#(config)#interface e0/0
R1#(config-if)ip address 10.0.0.1 255.255.255.252
R2#(config-if)#no shutdown
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_15_R1_b.jpg "R1")

c.	Configure a default route on each router pointed to the IP address of G0/0/0 (e0/0)on the other router.  
on R2 we will do
```
R2#(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_15_R2_c.jpg "R2")

on R1 we will do
```
R1#(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

d.	Verify static routing is working by pinging R2’s G0/0/1 (e0/1)address from R1.
```
R1#show ip route
R1#ping 192.168.1.97

```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_15_R1_c.jpg "R1 Verify static routing")


e.	Save the running configuration to the startup configuration file


#### 1.6 Configure basic settings for each switch.
a.	Assign a device name to the switch.  
b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
c.	Assign ___class___ as the privileged EXEC encrypted password.  
d.	Assign ___cisco___ as the console password and enable login.  
e.	Assign ___cisco___ as the VTY password and enable login.  
f.	Encrypt the plaintext passwords.  
g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.  
h.	Save the running configuration to the startup configuration file.  
i.	Set the clock on the switch to today’s time and date.  
Note: Use the question mark (?) to help with the correct sequence of parameters needed to execute this command.  
j.	Copy the running configuration to the startup configuration.  

![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_16_S1_basic.jpg "S1 basic settings")

![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_16_S2_basic.jpg "S2 basic settings")

#### 1.7: Create VLANs on S1.
___Note: S2 is only configured with basic settings.___  

a.	Create and name the required VLANs on switch 1 from the table above.  
```
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200
S1(config-vlan)#name Management
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
```

b.	Configure and activate the management interface on S1 (VLAN 200) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S1.  
```
S1(config)#interface vlan 200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.65
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_17_S1_ab.jpg "")


c.	Configure and activate the management interface on S2 (VLAN 1) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S2  
```
S2(config)#interface vlan 1
S2(config-if)#ip address 192.1638.1.98 255.255.255.240
S2(config-if)#no shutdown
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.1.97
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_17_S2_c.jpg "")

d.	Assign all unused ports on S1 to the Parking_Lot VLAN, configure them for static access mode, and administratively deactivate them. On S2, administratively deactivate all the unused ports.  

|Device	|Unused ports|
|:------|:--------------|
|S1		|e0/0, e0/2, e1/1-4 |
|S2		|e0/0-3, e1/0, e1/2|

for S1 we use next commands
```
S1(config)#interface range e0/0, e0/2, e1/0-3
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#shutdown
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_17_S1_d.jpg "")

for S2 we use next commands
```
S2(config)#interface range e0/0-3, e1/0, e1/2
S2(config-if-range)#shutdown
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_17_S2_d.jpg "")


#### 1.8: Assign VLANs to the correct switch interfaces.
a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode.  
```
S1(config)#interface e0/3
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 100

```

b.	Verify that the VLANs are assigned to the correct interfaces.  
```
S1#sh vlan brief
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_18_S1_b.jpg "")

**Question:**  
Why is interface F0/5 listed under VLAN 1?  
**Answer:**  
We dont add this interface to any VLAN and as default its using VLAN1.



#### 1.9: Manually configure S1’s interface F0/5 as an 802.1Q trunk.
a.	Change the switchport mode on the interface to force trunking.  
```
S1(config)#interface e0/1
S1(config-if)#switchport trunk encapsulation dot1q
S1(config-if)#switchport mode trunk
```
b.	As a part of the trunk configuration, set the native VLAN to 1000.  
```
S1(config-if)#switchport trunk native vlan 1000
```
c.	As another part of trunk configuration, specify that VLANs 100, 200, and 1000 are allowed to cross the trunk.  
```
S1(config-if)#switchport trunk allowed vlan 100,200,1000
```
d.	Save the running configuration to the startup configuration file.  

![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_19_S1_abcd.jpg "")

e.	Verify trunking status.
```
S1# show interfaces trunk
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_19_S1_e.jpg "")


**Question:**  At this point, what IP address would the PC’s have if they were connected to the network using DHCP?  
**Answer:**  

----
### Part 2: Configure and verify two DHCPv4 Servers on R1
Now we will configure and verify a DHCPv4 Server on R1. The DHCPv4 server will service two subnets, Subnet A and Subnet C.

#### 2.1: Configure R1 with DHCPv4 pools for the two supported subnets. Only the DHCP Pool for subnet A is given below

a.	Exclude the first five useable addresses from each address pool.  
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
```

b.	Create the DHCP pool (Use a unique name for each pool).  
```
R1(config)#ip dhcp pool VLAN100
```

c.	Specify the network that this DHCP server is supporting
```
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
```

d.	Configure the domain name as ***ccna-lab.com***
```
R1(dhcp-config)#domain-name ccna-lab.com
```

e.	Configure the appropriate default gateway for each DHCP pool.
```
R1(dhcp-config)#default-router 192.168.1.1
```

f.	Configure the lease time for 2 days 12 hours and 30 minutes.
```
R1(dhcp-config)#lease 2 12 30
```

g.	Next, configure the second DHCPv4 Pool using the pool name R2_Client_LAN and the calculated network, default-router and use the same domain name and lease time from the previous DHCP pool.

```
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#lease 2 12 30
```
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_21_R1_abcdefg.jpg "")

#### 2.2: Save your configuration
Save the running configuration to the startup configuration file.



#### 2.3: Verify the DHCPv4 Server configuration
a.	Issue the command **show ip dhcp pool** to examine the pool details.  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_23_R1_a.jpg "")

b.	Issue the command **show ip dhcp binding** to examine established DHCP address assignments.  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_23_R1_b.jpg "")

c.	Issue the command **show ip dhcp server statistics** to examine DHCP messages.  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_23_R1_c.jpg "")

#### 2.4: Attempt to acquire an IP address from DHCP on PC-A
a.	Open a command prompt on PC-A and issue the command ***ipconfig /renew.***  
b.	Once the renewal process is complete, issue the command ***ipconfig*** to view the new IP information.  
c.	Test connectivity by pinging R1’s G0/0/1 (e0/1) interface IP address (192.168.1.1)  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_24_PCA_c.jpg "")

-----
### 3: Configure and verify a DHCP Relay on R2

We will configure R2 to relay DHCP requests from the local area network on interface G0/0/1 to the DHCP server (R1). 

#### 3.1: Configure R2 as a DHCP relay agent for the LAN on G0/0/1
a.	Configure the **ip helper-address** command on G0/0/1 specifying R1’s G0/0/0 IP address.
```
R2(config)#int e0/1
R2(config-if)#ip helper-address 10.0.0.1
```
b.	Save your configuration.  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_31_R2_ab.jpg "")

#### 3.2: Attempt to acquire an IP address from DHCP on PC-B
a.	Open a command prompt on PC-B and issue the command **ipconfig /renew.**  
b.	Once the renewal process is complete, issue the command **ipconfig** to view the new IP information.  
c.	Test connectivity by pinging R1’s G0/0/1 interface IP address.  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_32_PCB_abc.jpg "")

d.	Issue the **show ip dhcp binding** on R1 to verify DHCP bindings. 
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_32_R1_d.jpg "") 

e.	Issue the **show ip dhcp server statistics** on R1 and R2 to verify DHCP messages.  
on R1
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_32_R1_e.jpg "") 

on R2
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic_32_R2_e.jpg "") 

