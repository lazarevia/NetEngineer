# Implement DHCPv4
	Топология
 ![](/Labworks/Lab_03/Implement_DHCPv4/pics/scheme.jpg "Топология")

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
|----|----|----
|R2		|G0/0/0			|e0/0			|
|		|G0/0/1			|e0/1			|
|----|----|----
|S1		|F0/5			|e0/1|
|		|F0/6			|e0/3|
|----|----|----
|S2		|F0/5			|e1/1|
|		|F0/18			|e1/3|


## Objectives
Part 1: Build the Network and Configure Basic Device Settings  
Part 2: Configure and verify two DHCPv4 Servers on R1  
Part 3: Configure and verify a DHCP Relay on R2  


----
### Part 1:	Build the Network and Configure Basic Device Settings  
In Part 1, you will set up the network topology and configure basic settings on the PC hosts and switches.

#### 1.1: Step 1: Establish an addressing scheme
Subnet the network 192.168.1.0/24 to meet the following requirements:
a.	One subnet, “Subnet A”, supporting 58 hosts (the client VLAN at R1).  
**Subnet A:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet A	|58			|192.168.1.0	|192.168.1.1	|192.168.1.62	|192.168.1.63	|255.255.255.192

Record the first IP address in the Addressing Table for R1 G0/0/1.100. 
192.168.1.1

Record the second IP address in the Address Table for S1 VLAN 200  
192.168.1.66  
and enter the associated default gateway.  
192.168.1.65


b.	One subnet, “Subnet B”, supporting 28 hosts (the management VLAN at R1).  
**Subnet B:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet B	|28			|192.168.1.64	|192.168.1.65	|192.168.1.94	|192.168.1.95	|255.255.255.224

Record the first IP address in the Addressing Table for R1 G0/0/1.200.  
192.168.1.65

Record the second IP address in the Address Table for S1 VLAN 1 and enter the associated default gateway.

c.	One subnet, “Subnet C”, supporting 12 hosts (the client network at R2).  
**Subnet C:**

|Subnet		|hosts		|net addr		|1st IP			|Last IP		|broadcast		|mask
|:---		|:---		|:---			|:---			|:---			|:---			|:---
|Subnet C	|12			|192.168.1.96	|192.168.1.97	|192.168.1.110	|192.168.1.111	|255.255.255.240

Record the first IP address in the Addressing Table for R2 G0/0/1.  
192.168.1.97


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
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic01_R1config.jpg "R1 config")

R2 basic settings  
![](/Labworks/Lab_03/Implement_DHCPv4/pics/pic01_R2config.jpg "R1 config")


#### 1.4:	Step 4: Configure Inter-VLAN Routing on R1
a.	Activate interface G0/0/1 on the router.  
```
R1(config)# interface g0/0/1
R1(config)# no shutdown
```

b.	Configure sub-interfaces for each VLAN as required by the IP addressing table. All sub-interfaces use 802.1Q encapsulation and are assigned the first usable address from the IP address pool you have calculated. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.  

```
R1(config)# interface g0/0/1.100
R1(config-subif)# description Client_Network
R1(config-subif)# encapsulation dot1q 100
R1(config-subif)# ip address 192.168.1.1 255.255.255.192
R1(config)# interface G0/0/1.200
R1(config-subif)# description Management
R1(config-subif)# encapsulation dot1q 200
R1(config-subif)# ip address 192.168.1.65 255.255.255.224
R1(config-subif)# exit
R1(config)# interface G0/0/1.1000
R1(config-subif)# description Native
R1(config-subif)# encapsulation dot1q 1000 native
R1(config-subif)# end
```

c.	Verify the sub-interfaces are operational.
```
R1# show ip interface brief
```


#### 1.5 Step 5: Configure G0/0/1 on R2, then G0/0/0 and static routing for both routers
a.	Configure G0/0/1 on R2 with the first IP address of Subnet C you calculated earlier.

```
R2#(config)#interface g0/0/1
R2#(config-if)#ip address 192.168.1.97 255.255.255.240
R2#(config-if)#no shutdown
R2#(config-if)#exit
2
```


b.	Configure interface G0/0/0 for each router based on the IP Addressing table above.  
On R2 we continue with next commands
```
R2#(config)#interface g0/0/0
R2#(config-if)#ip address 10.0.0.2 255.255.255.252
R2#(config-if)#no shutdown
```

on R1 we will continue with
```
R1#conf t
R1#(config)#interface g0/0/0
R1#(config-if)ip address 10.0.0.1 255.255.255.252
R2#(config-if)#no shutdown
```

c.	Configure a default route on each router pointed to the IP address of G0/0/0 on the other router.
on R2 we will do
```
R2#(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
on R2 we will do
```
R1#(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

d.	Verify static routing is working by pinging R2’s G0/0/1 address from R1.
```
R1#show ip route
R1#ping 192.168.1.97

```
e.	Save the running configuration to the startup configuration file


#### 1.6: Configure basic settings for each switch.
a.	Assign a device name to the switch.
b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.
c.	Assign class as the privileged EXEC encrypted password.
d.	Assign cisco as the console password and enable login.
e.	Assign cisco as the VTY password and enable login.
f.	Encrypt the plaintext passwords.
g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.
h.	Save the running configuration to the startup configuration file.
i.	Set the clock on the switch to today’s time and date.
Note: Use the question mark (?) to help with the correct sequence of parameters needed to execute this command.
j.	Copy the running configuration to the startup configuration.


Step 7: Create VLANs on S1.
Note: S2 is only configured with basic settings.
a.	Create and name the required VLANs on switch 1 from the table above.
b.	Configure and activate the management interface on S1 (VLAN 200) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S1.
c.	Configure and activate the management interface on S2 (VLAN 1) using the second IP address from the subnet calculated earlier. Additionally, set the default gateway on S2
d.	Assign all unused ports on S1 to the Parking_Lot VLAN, configure them for static access mode, and administratively deactivate them. On S2, administratively deactivate all the unused ports.
Note: The interface range command is helpful to accomplish this task with as few commands as necessary.
Close configuration window
Open configuration window
Close configuration window

Step 8: Assign VLANs to the correct switch interfaces.
a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode.
Open configuration window
b.	Verify that the VLANs are assigned to the correct interfaces.
Question:
Why is interface F0/5 listed under VLAN 1?
Type your answers here.

Step 9: Manually configure S1’s interface F0/5 as an 802.1Q trunk.
a.	Change the switchport mode on the interface to force trunking.
b.	As a part of the trunk configuration, set the native VLAN to 1000.
c.	As another part of trunk configuration, specify that VLANs 100, 200, and 1000 are allowed to cross the trunk.
d.	Save the running configuration to the startup configuration file.
e.	Verify trunking status.
Question:
At this point, what IP address would the PC’s have if they were connected to the network using DHCP?





