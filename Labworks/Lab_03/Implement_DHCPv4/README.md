# Configure DHCPv6
	Task topology
 ![](/Labworks/Lab_03/Configure_DHCPv6/pics/scheme.png "Task topology")

	Addressing Table
|Device 		|Interface		|IPv6 Address		|
|:--------------|:--------------|:--------------|
|R1				|G0/0/0			|2001:db8:acad:2::1 /64	|
|				|				|fe80::1		|
|				|G0/0/1			|2001:db8:acad:1::1/64		|
|				|				|fe80::1			|
|R2				|G0/0/0			|2001:db8:acad:2::2/64		|
|				|				|fe80::2		|
|				|G0/0/1			|2001:db8:acad:3::1 /64			|
|				|				|fe80::1			|
|PC-A			|NIC			|DHCP			|
|PC-B 			|NIC			|DHCP			|


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
 ![](/Labworks/Lab_03/Configure_DHCPv6/pics/pic0_scheme.jpg "Solvation topology")



## Objectives
Part 1: Build the Network and Configure Basic Device Settings  
Part 2: Verify SLAAC address assignment from R1  
Part 3: Configure and verify a Stateless DHCPv6 Server on R1  
Part 4: Configure and verify a Stateful DHCPv6 Server on R1  
Part 5: Configure and verify a DHCPv6 Relay on R2  


----
### Part 1:	Build the Network and Configure Basic Device Settings  
In Part 1, we will set up the network topology and configure basic settings on the PC hosts and switches.

#### 1.1: Cable the network as shown in the topology.
Attach the devices as shown in the topology diagram, and cable as necessary.


#### 1.2: Configure basic settings for each switch. (Optional)
a.	Assign a device name to the switch.
b.	Disable DNS lookup to prevent the router from attempting to translate incorrectly entered commands as though they were host names.
c.	Assign class as the privileged EXEC encrypted password.
d.	Assign cisco as the console password and enable login.
e.	Assign cisco as the VTY password and enable login.
f.	Encrypt the plaintext passwords.
g.	Create a banner that warns anyone accessing the device that unauthorized access is prohibited.
h.	Shutdown all unused ports
i.	Save the running configuration to the startup configuration file.
