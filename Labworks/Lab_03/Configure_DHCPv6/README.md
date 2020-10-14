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
[Part 1: Build the Network and Configure Basic Device Settings](#part-1-build-the-network-and-configure-basic-device-settings)  
[Part 2: Verify SLAAC address assignment from R1](#part-2-verify-slaac-address-assignment-from-r1)  
[Part 3: Configure and verify a Stateless DHCPv6 Server on R1](#part-3-configure-and-verify-a-dhcpv6-server-on-r1)  
[Part 4: Configure and verify a Stateful DHCPv6 Server on R1](#part-4-configure-a-stateful-dhcpv6-server-on-r1)  
[Part 5: Configure and verify a DHCPv6 Relay on R2](#part-5-configure-and-verify-dhcpv6-relay-on-r2)  


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

----

### Part 2: Verify SLAAC Address Assignment from R1
In Part 2, you will verify that Host PC-A receives an IPv6 address using the SLAAC method.
Power PC-A up and ensure that the NIC is configured for IPv6 automatic configuration.
After a few moments, the results of the command ipconfig should show that PC-A has assigned itself an address from the 2001:db8:1::/64 network.
```
C:\Users\Student> ipconfig
Windows IP Configuration

Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : 
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:5c43:ee7c:2959:da68
   Temporary IPv6 Address. . . . . . : 2001:db8:acad:1:3c64:e4f9:46e1:1f23
   Link-local IPv6 Address . . . . . : fe80::5c43:ee7c:2959:da68%6
   IPv4 Address. . . . . . . . . . . : 169.254.218.104
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::1%6
```
**Question:**  
Where did the host-id portion of the address come from?

**Answer:**

----
### Part 3: Configure and Verify a DHCPv6 server on R1
In Part 3, you will configure and verify a stateless DHCP server on R1. The objective is to provide PC-A with DNS server and Domain information.

#### 3.1: Examine the configuration of PC-A in more detail.
a.	Issue the command ipconfig /all on PC-A and take a look at the output.
```
C:\Users\Student> ipconfig /all
Windows IP Configuration

   Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : 
   Description . . . . . . . . . . . : Intel(R) 852574L Gigabit Network Connection
   Physical Address. . . . . . . . . : 00-50-56-83-63-6D
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:5c43:ee7c:2959:da68(Preferred)
   Temporary IPv6 Address. . . . . . : 2001:db8:acad:1:3c64:e4f9:46e1:1f23(Preferred)
   Link-local IPv6 Address . . . . . : fe80::5c43:ee7c:2959:da68%6(Preferred)
   IPv4 Address. . . . . . . . . . . : 169.254.218.104(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::1%6
   DHCPv6 IAID . . . . . . . . . . . : 50334761
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F5-CE-A2-00-50-56-B3-63-6D
   DNS Servers . . . . . . . . . . . : fec0:0:0:ffff::1%1
                                       fec0:0:0:ffff::2%1
                                       fec0:0:0:ffff::3%1
   NetBIOS over Tcpip. . . . . . . . : Enabled
```   
b.	Notice that there is no Primary DNS suffix. Also note that the DNS server addresses provided are “site local anycast” addresses, and not unicast addresses, as would be expected.

#### 3.2: Configure R1 to provide stateless DHCPv6 for PC-A.
a.	Create an IPv6 DHCP pool on R1 named R1-STATELESS. As a part of that pool, assign the DNS server address as 2001:db8:acad::1 and the domain name as stateless.com.
Open configuration window
```
R1(config)# ipv6 dhcp pool R1-STATELESS
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATELESS.com
```

b.	Configure the G0/0/1 interface on R1 to provide the OTHER config flag to the R1 LAN, and specify the DHCP pool you just created as the DHCP resource for this interface.
```
R1(config)# interface g0/0/1
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server R1-STATELESS
```

c.	Save the running configuration to the startup configuration file.
Close configuration window
d.	Restart PC-A.
e.	Examine the output of ipconfig /all and notice the changes.
```
C:\Users\Student> ipconfig /all
Windows IP Configuration

   Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : STATELESS.com

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : STATELESS.com
   Description . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection
   Physical Address. . . . . . . . . : 00-50-56-83-63-6D
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:5c43:ee7c:2959:da68(Preferred)
   Temporary IPv6 Address. . . . . . : 2001:db8:acad:1:3c64:e4f9:46e1:1f23(Preferred)
   Link-local IPv6 Address . . . . . : fe80::5c43:ee7c:2959:da68%6(Preferred)
   IPv4 Address. . . . . . . . . . . : 169.254.218.104(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::1%6
   DHCPv6 IAID . . . . . . . . . . . : 50334761
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F5-CE-A2-00-50-56-B3-63-6D
   DNS Servers . . . . . . . . . . . : 2001:db8:acad::254
   NetBIOS over Tcpip. . . . . . . . : Enabled
   Connection-specific DNS Suffix Search List :
                                       STATELESS.com
```									   
f.	Test connectivity by pinging R2’s G0/0/1 interface IP address.

----
### Part 4: Configure a stateful DHCPv6 server on R1
In Part 4, you will configure R1 to respond to DHCPv6 requests from the LAN on R2.

a.	Create a DHCPv6 pool on R1 for the 2001:db8:acad:3:aaaa::/80 network. This will provide addresses to the LAN connected to interface G0/0/1 on R2. As a part of the pool, set the DNS server to 2001:db8:acad::254, and set the domain name to STATEFUL.com.
Open configuration window
```
R1(config)# ipv6 dhcp pool R2-STATEFUL
R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATEFUL.com
```
b.	Assign the DHCPv6 pool you just created to interface g0/0/0 on R1.
```
R1(config)# interface g0/0/0
R1(config-if)# ipv6 dhcp server R2-STATEFUL
```

----
### Part 5: Configure and verify DHCPv6 relay on R2.
In Part 5, you will configure and verify DHCPv6 relay on R2, allowing PC-B to receive an IPv6 Address.

#### 5.1: Power on PC-B and examine the SLAAC address that it generates.
```
C:\Users\Student> ipconfig /all
Windows IP Configuration

   Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : 
   Description . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection
   Physical Address. . . . . . . . . : 00-50-56-B3-7B-06
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad:3:a0f3:3d39:f9fb:a020(Preferred)
   Temporary IPv6 Address. . . . . . : 2001:db8:acad:3:d4f3:7b16:eeee:b2b5(Preferred)
   Link-local IPv6 Address . . . . . : fe80::a0f3:3d39:f9fb:a020%6(Preferred)
   IPv4 Address. . . . . . . . . . . : 169.254.160.32(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::1%6
   DHCPv6 IAID . . . . . . . . . . . : 50334761
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F2-08-38-00-50-56-B3-7B-06
   DNS Servers . . . . . . . . . . . : fec0:0:0:ffff::1%1
                                       fec0:0:0:ffff::2%1
                                       fec0:0:0:ffff::3%1
   NetBIOS over Tcpip. . . . . . . . : Enabled
```
Notice in the output that the prefix used is 2001:db8:acad:3::

#### 5.2: Configure R2 as a DHCP relay agent for the LAN on G0/0/1.

a.	Configure the ipv6 dhcp relay command on R2 interface G0/0/1, specifying the destination address of the G0/0/0 interface on R1. Also configure the managed-config-flag command.
Open configuration window
```
R2(config)# interface g0/0/1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
```
b.	Save your configuration.
Close configuration window

#### 5.3: Attempt to acquire an IPv6 address from DHCPv6 on PC-B.
a.	Restart PC-B.
b.	Open a command prompt on PC-B and issue the command ipconfig /all and examine the output to see the results of the DHCPv6 relay operation.
```
C:\Users\Student> ipconfig /all
Windows IP Configuration

   Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : STATEFUL.com

Ethernet adapter Ethernet0:

   Connection-specific DNS Suffix  . : STATEFUL.com
   Description . . . . . . . . . . . : Intel(R) 852574L Gigabit Network Connection
   Physical Address. . . . . . . . . : 00-50-56-B3-7B-06
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv6 Address. . . . . . . . . . . : 2001:db8:acad3:aaaa:7104:8b7d:5402(Preferred)
   Lease Obtained. . . . . . . . . . : Sunday, October 6, 2019 3:27:13 PM
   Lease Expires . . . . . . . . . . : Tuesday, October 8, 2019 3:27:13 PM
   Link-local IPv6 Address . . . . . : fe80::a0f3:3d39:f9fb:a020%6(Preferred)
   IPv4 Address. . . . . . . . . . . : 169.254.160.32(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.0.0
   Default Gateway . . . . . . . . . : fe80::2%6
   DHCPv6 IAID . . . . . . . . . . . : 50334761
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F2-08-38-00-50-56-B3-7B-06
   DNS Servers . . . . . . . . . . . : 2001:db8:acad::254
   NetBIOS over Tcpip. . . . . . . . : Enabled
   Connection-specific DNS Suffix Search List  :
                                       STATEFUL.com
```									   
c.	Test connectivity by pinging R1’s G0/0/1 interface IP address.


