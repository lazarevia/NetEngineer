# Lab 1 - Configure Router-on-a-Stick Inter-VLAN Routing

[TOC]
	
	Topology

 ![](/Labworks/Lab_01/img/scheme.JPG "Topology")
 
	Addressing Table

|Device	|Interface	|IP Address		|Subnet	Mask	|Default Gateway|
|:------|:----------|:--------------|:--------------|:---------------
|R1		|G0/0/1.3	|192.168.3.1	|255.255.255.0	|N/A
|R1 	|G0/0/1.4	|192.168.4.1	|255.255.255.0	|N/A
|R1		|G0/0/1.8	|N/A		 	|N/A			|N/A
|S1		|VLAN 3		|192.168.3.11 	|255.255.255.0	|192.168.3.1
|S2		|VLAN 3		|192.168.3.12	|255.255.255.0	|192.168.3.1
|PC-A 	|NIC		|192.168.3.3	|255.255.255.0	|192.168.3.1
|PC-B	|NIC		|192.168.4.3	|255.255.255.0	|192.168.4.1

	VLAN Table
	

|VLAN 	|Name 			|Interface Assigned
|:------|:--------------|:-----------------
|3 		|Management		|S1: VLAN 3
|		|				|S2: VLAN 3
|		|				|S1: F0/6
|4		|Operations		|S2: F0/18
|7		|ParkingLot		|S1: F0/2-4, F0/7-24, G0/1-2 
|		|				|S2: F0/2-17, F0/19-24, G0/1-2 
|8		|Native			|N/A

	Table of compliance of ports of the task and virtual environment

|Device	|Task Interface	|Lab Interface	|
|:------|:--------------|:--------------|
|R1		|G0/0/1			|e0/0			|
|		|G0/0/1.3		|e0/0.3			|
|		|G0/0/1.4		|e0/0.4			|
|		|G0/0/1.8		|e0/0.8			|
|S1		|F0/1			|e0/1|
|		|F0/5			|e0/3|
|		|F0/6			|e1/3|
|S2		|F0/1			|e0/1|
|		|F0/18			|e1/3|



## Objectives
1. Build the Network and Configure Basic Device Settings.
2. Create VLANs and Assign Switch Ports.
3. Configure an 802.1Q Trunk between the Switches.
4. Configure Inter-VLAN Routing on the Router.
5. Verify Inter-VLAN Routing is working.


## Instructions
### 1. Build the Network and Configure Basic Device Settings
In Part 1, you will set up the network topology and configure basic settings on the PC hosts and switches.

#### 1.1. Cable the network as shown in the topology.
Attach the devices as shown in the topology diagram, and cable as necessary.

#### 1.2. Configure basic settings for the router.

__Open configuration window__  
a. 	Console into the router and enable privileged EXEC mode.  
b.	Enter configuration mode.  
c.	Assign a device name to the router.  
```
Switch(config)# hostname R1
R1(config)#
```
d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
```
R1(config)# no ip domain-lookup
R1(config)#
```
e.	Assign ___class___ as the privileged EXEC encrypted password.  
```
R1(config)# service password-encryption
R1(config)#
R1(config)# enable secret class
R1(config)#
```
f.	Assign ___cisco___ as the console password and enable login.  
```
R1(config)# line con 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# logging synchronous
R1(config-line)# exit
R1(config)#
```
g.	Assign cisco as the VTY password and enable login.  
```
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# end
```
h.	Encrypt the plaintext passwords. 
```
R1(config)# service password-encryption
```
i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.  
```
R1(config)# banner motd #
Enter Text message. End with the character ‘#’.
Unauthorized access is strictly prohibited. #
```
j.	Save the running configuration to the startup configuration file.  
![](/Labworks/Lab_01/img/pic01.JPG "Basic settings")

k.	Set the clock on the router.  
![](/Labworks/Lab_01/img/pic02.JPG "Set clock")  
__Close configuration window__

#### 1.3. Configure basic settings for each switch.

__Open configuration window__  

a.	Console into the switch and enable privileged EXEC mode.  
b.	Enter configuration mode.  
c.	Assign a device name to the switch.  
d.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.  
e.	Assign class as the privileged EXEC encrypted password.  
f.	Assign cisco as the console password and enable login.  
g.	Assign cisco as the vty password and enable login.  
h.	Encrypt the plaintext passwords.  
i.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.  
j.	Set the clock on the switch.  
k.	Copy the running configuration to the startup configuration.  

__Close configuration window__

![](/Labworks/Lab_01/img/pic03.JPG "Basic settings for S2")

#### 1.4: Configure PC hosts.
Refer to the Addressing Table for PC host address information.

## 2: Create VLANs and Assign Switch Ports
In Part 2, you will create VLANs, as specified in the table above, on both switches. You will then assign the VLANs to the appropriate interface. The show vlan command is used to verify your configuration settings. Complete the following tasks on each switch.

### 2.1: Create VLANs on both switches.
Open configuration window  
a.	Create and name the required VLANs on each switch from the table above.  
![](/Labworks/Lab_01/img/pic04.JPG "Creating and naming VLANs")

b.	Configure the management interface and default gateway on each switch using the IP address information in the Addressing Table.   
```
S1(config)# interface vlan 3
S1(config-if)# ip address 192.168.3.11 255.255.255.0
S1(config-if)# no shutdown
```
Default gateway
```
S1(config)# ip default-gateway  192.168.3.1
```
![](/Labworks/Lab_01/img/pic05_S1.jpg "S1: IP address and default gateway")

![](/Labworks/Lab_01/img/pic05_S2.jpg "S2: IP address and default gateway")

c.	Assign all unused ports on both switches to the ParkingLot VLAN, configure them for static access mode, and administratively deactivate them.  
	
	Unused ports

|Device	|Interface	|
|:------|:--------------|
|S1		|e0/0|
|		|e0/2|
|		|e1/0|
|		|e1/1|
|		|e1/2|
|S2		|e0/0|
|		|e0/2|
|		|e0/3|
|		|e1/0|
|		|e1/1|
|		|e1/2|

***Note:*** The interface range command is helpful to accomplish this task with as few commands as necessary.  

```
S1(config)# interface range {range}
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 7
```

	S1 unused ports
![](/Labworks/Lab_01/img/pic06_S1.jpg "S1 unused ports")

	S2 unused ports
![](/Labworks/Lab_01/img/pic06_S2.jpg "S2 unused ports")

#### 2.2: Assign VLANs to the correct switch interfaces.
a.	Assign used ports to the appropriate VLAN (specified in the VLAN table above) and configure them for static access mode. Be sure to do this on both switches

```
S2(config)# interface e1/3
S2(config-if)# switchport mode access
S2(config-if)# switchport access vlan 3
```
b.	Issue the ___show vlan brief___ command and verify that the VLANs are assigned to the correct interfaces.
![](/Labworks/Lab_01/img/pic07_S1.jpg "S1 used ports")

![](/Labworks/Lab_01/img/pic07_S2.jpg "S2 used ports")

__Close configuration window__

### 3: Configure an 802.1Q Trunk Between the Switches
In Part 3, you will manually configure interface F0/1 as a trunk.

#### 3.1: Manually configure trunk interface F0/1.
__Open configuration window__  
a.	Change the switchport mode on interface F0/1 to force trunking. Make sure to do this on both switches.
```
S1(config)# interface e0/1
S1(config-if)# switchport trunk encapsulation dot1q
S1(config-if)# switchport mode trunk
```
b.	As a part of the trunk configuration, set the native VLAN to 8 on both switches. You may see error messages temporarily while the two interfaces are configured for different native VLANs.

```
S1(config-if)# switchport trunk native vlan 8
```
c.	As another part of trunk configuration, specify that VLANs 3, 4, and 8 are only allowed to cross the trunk.
```
S1(config-if)# switchport trunk allowed vlan 3,4,8
```
d.	Issue the ___show interfaces trunk___ command to verify trunking ports, the Native VLAN and allowed VLANs across the trunk.

![](/Labworks/Lab_01/img/pic08_S1.jpg "S1 show interfaces trunk")

![](/Labworks/Lab_01/img/pic08_S2.jpg "S2 show interfaces trunk")

#### 3.2: Manually configure S1’s trunk interface F0/5
a.	Configure the F0/5 on S1 with the same trunk parameters as F0/1. This is the trunk to the router.
b.	Save the running configuration to the startup configuration file on S1 and S2.
c.	Issue the ___show interfaces trunk___ command to verify trunking.

![](/Labworks/Lab_01/img/pic09_S1.jpg "S1 show interfaces trunk")


**Question:** Why does F0/5 (e0/3) not appear in the list of trunks?  
**Answer:** We dont see this interface because G0/0/1 (e0/0) on a router is administratively down.

__Close configuration window__


#### 4: Configure Inter-VLAN Routing on the Router
https://youtu.be/PbeWe5Jaunk?t=1497
Open configuration window
a.	Activate interface G0/0/1 on the router.
b.	Configure sub-interfaces for each VLAN as specified in the IP addressing table. All sub-interfaces use 802.1Q encapsulation. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.  
```
R1#conf term
R1(config)#int e0/0.3
R1(config-subif)#encapsulation dot1q 3
R1(config-subif)#ip address 192.168.3.1 255.255.255.0
R1(config-subif)#description Management
R1(config-subif)#int e0/0.4
R1(config-subif)#encapsulation dot1q 4
R1(config-subif)#ip address 192.168.4.1 255.255.255.0
R1(config-subif)#description Operations
R1(config-subif)#int e0/0.8
R1(config-subif)#encapsulation dot1q 8 native
R1(config-subif)#description Native
R1(config-subif)#int e0/0
R1(config-if)#no shutdown
R1(config-if)#end
```

![](/Labworks/Lab_01/img/pic10_R1.jpg "R1 Configure sub-interfaces"  
c.	Use the ___show ip interface brief___ command to verify the sub-interfaces are operational.
)  
![](/Labworks/Lab_01/img/pic11_R1.jpg "R1 show ip interface brief_"  
Close configuration window



### 5: Verify Inter-VLAN Routing is Working
#### 5.1: Complete the following tests from PC-A. All should be successful.
Note: You may have to disable the PC firewall for pings to be successful.
a.	Ping from PC-A to its default gateway.
b.	Ping from PC-A to PC-B
c.	Ping from PC-A to S2
#### 5.2: Complete the following test from PC-B.
From the command prompt on PC-B, issue the tracert command to the address of PC-A.

**Question**
:	What intermediate IP addresses are shown in the results?
Type your answers here.

Router Interface Summary Table
|Router Model	|Ethernet Interface #1		|Ethernet Interface #2		|Serial Interface #1	|Serial Interface #2
|:				|:							|:							|:						|:
|1800			|Fast Ethernet 0/0 (F0/0) 	|Fast Ethernet 0/1 (F0/1) 	|Serial 0/0/0 (S0/0/0)	|Serial 0/0/1 (S0/0/1)
|1900			|Gigabit Ethernet 0/0 (G0/0)|Gigabit Ethernet 0/1 (G0/1)|Serial 0/0/0 (S0/0/0)	|Serial 0/0/1 (S0/0/1)
|2801			|Fast Ethernet 0/0 (F0/0)	|Fast Ethernet 0/1 (F0/1)	|Serial 0/1/0 (S0/1/0)	|Serial 0/1/1 (S0/1/1)
2811	Fast Ethernet 0/0 (F0/0)	Fast Ethernet 0/1 (F0/1)	Serial 0/0/0 (S0/0/0)	Serial 0/0/1 (S0/0/1)
2900	Gigabit Ethernet 0/0 (G0/0)	Gigabit Ethernet 0/1 (G0/1)	Serial 0/0/0 (S0/0/0)	Serial 0/0/1 (S0/0/1)
4221	Gigabit Ethernet 0/0/0 (G0/0/0)	Gigabit Ethernet 0/0/1 (G0/0/1)	Serial 0/1/0 (S0/1/0)	Serial 0/1/1 (S0/1/1)
4300	Gigabit Ethernet 0/0/0 (G0/0/0)	Gigabit Ethernet 0/0/1 (G0/0/1)	Serial 0/1/0 (S0/1/0)	Serial 0/1/1 (S0/1/1)

Note: To find out how the router is configured, look at the interfaces to identify the type of router and how many interfaces the router has. There is no way to effectively list all the combinations of configurations for each router class. This table includes identifiers for the possible combinations of Ethernet and Serial interfaces in the device. The table does not include any other type of interface, even though a specific router may contain one. An example of this might be an ISDN BRI interface. The string in parenthesis is the legal abbreviation that can be used in Cisco IOS commands to represent the interface.
End of Document