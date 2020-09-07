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


	Таблица VLAN

|VLAN 			|Name			|Interface Assigned
|:--------------|:--------------|:--------------|
|1				|N/A			|S2: F0/18
|100			|Клиенты		|S1: F0/6
|200			|Управление 	|S1: VLAN 200
|999			|Parking_Lot	|S1: F0/1-4, F0/7-24, G0/1-2
|1000			|Native 		|N/A


## Objectives
Part 1: Build the Network and Configure Basic Device Settings
Part 2: Configure and verify two DHCPv4 Servers on R1
Part 3: Configure and verify a DHCP Relay on R2


----
### 1:	Построить Сеть и произвести базовую настроеку устройств
В части 1 вам предстоит настроить топологию сети и настроить основные параметры сетевых устройств.

#### 1.1: Establish an addressing scheme
Разделить сеть 192.168.1.0/24 на подсети по следующим требованиям:
а. Одна подсеть "Subnet A" поддерживает 58 хостов (клиентский VLAN на R1)
Subnet A:

Record the first IP address in the Addressing Table for R1 G0/0/1.100. 
Record the second IP address in the Address Table for S1 VLAN 200 and enter the associated default gateway.

б. Одна подсеть "Subnet B" поддерживает 28 хостов.(VLAN управления на R1)
Subnet B:

Record the first IP address in the Addressing Table for R1 G0/0/1.200. 
Record the second IP address in the Address Table for S1 VLAN 1 and enter the associated default gateway.

c.	Одна подсеть “Subnet C” поддерживает 12 хостов (клиентский VLAN на R2).
Subnet C:

Record the first IP address in the Addressing Table for R2 G0/0/1
Запишите первый IP-адрес в таблицу адресации для R2 G0/0/1


#### 1.2: Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### 1.3: Произведите настройку базовых параметров для каждого маршрутизатора.
a.	Присвойте имя устройства
b.	Отключите поиск DNS.
c.	Назначьте "class" в качестве зашифрованного пароля доступа к привилегированному режиму.
d.	Назначьте "cisco" в качестве пароля консоли и активируйте логин.
e.	Назначьте "cisco" в качестве пароля VTY и активируйте логин.
f.	Encrypt the plaintext passwords.
g.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
i.	Установите часы роутера на текущие дату и время.

a.	Присвойте имена устройствам в соответствии с топологией.
```
Switch(config)# hostname R1
S1(config)#
```

b.	Отключите поиск DNS.
```
Switch(config)# no ip domain-lookup
Switch(config)#
```

c.	Назначьте ___class___ в качестве зашифрованного пароля доступа к привилегированному режиму.
```
R1(config)# service password-encryption
R1(config)#
R1(config)# enable secret class
R1(config)#
```

d.	Назначьте ___cisco___ в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.
```
S1(config)# line vty 0 4
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# end
```

e.	Настройте logging synchronous для консольного канала.
```
S1(config)# line con 0
S1(config-line)# password cisco
S1(config-line)# login
S1(config-line)# logging synchronous
S1(config-line)# exit
S1(config)#
```

f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.
```
S1(config)# banner motd #
Enter Text message. End with the character ‘#’.
Unauthorized access is strictly prohibited. #
```
![](/Labworks/Lec-03_Lab03/img/pic1.jpg "Результат выполенения")

g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.
```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 1
S1(config-vlan)#exit
S1(config)#int vlan 1
S1(config-if)#ip
*Apr 24 11:42:26.655: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to down
S1(config-if)#ip address 192.16.1.1 255.255.255.0
S1(config-if)#no shutdown
S1(config-if)#
*Apr 24 11:43:00.022: %LINK-3-UPDOWN: Interface Vlan1, changed state to up
*Apr 24 11:43:01.029: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
S1(config-if)#exit
S1(config)#
```

![](/Labworks/Lec-03_Lab03/img/pic4.jpg "")

h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.
Командами `write memory` или `copy running-config startup-config`

#### 1.4:	Step 4: Configure Inter-VLAN Routing on R1
a.	Activate interface G0/0/1 on the router.
b.	Configure sub-interfaces for each VLAN as required by the IP addressing table. All sub-interfaces use 802.1Q encapsulation and are assigned the first usable address from the IP address pool you have calculated. Ensure the sub-interface for the native VLAN does not have an IP address assigned. Include a description for each sub-interface.
c.	Verify the sub-interfaces are operational.

#### 1.5 Step 5: Configure G0/0/1 on R2, then G0/0/0 and static routing for both routers
a.	Configure G0/0/1 on R2 with the first IP address of Subnet C you calculated earlier.
b.	Configure interface G0/0/0 for each router based on the IP Addressing table above.
c.	Configure a default route on each router pointed to the IP address of G0/0/0 on the other router.
d.	Verify static routing is working by pinging R2’s G0/0/1 address from R1.
e.	Save the running configuration to the startup configuration file

#### 1.6 Step 6: Configure basic settings for each switch.
a.	Assign a device name to the switch.
Open configuration window
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





----

### 2. Выбор корневого моста

#### 2.1:	Отключите все порты на коммутаторах.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int range e0/0-3
S1(config-if-range)#shutdown
S1(config-if-range)#end
S1#
```
![](/Labworks/Lec-03_Lab03/img/pic5_S1.jpg "выключение портов на S1")

![](/Labworks/Lec-03_Lab03/img/pic5_S2.jpg "выключение портов на S2")

![](/Labworks/Lec-03_Lab03/img/pic5_S3.jpg "выключение портов на S3")

#### 2.2:	Настройте подключенные порты в качестве транковых.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int range e0/0-3
S1(config-if-range)#switchport trunk encapsulation dot1q
S1(config-if-range)#switchport mode trunk
S1(config-if-range)#end
S1#

```

#### 2.3:	Включите порты F0/2 и F0/4(e0/1 и e0/3) на всех коммутаторах.

```
S3(config)#int e0/1
S3(config-if)#no shutdown
S3(config-if)#
*Apr 27 16:05:18.045: %LINK-3-UPDOWN: Interface Ethernet0/1, changed state to up
*Apr 27 16:05:19.048: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to up
S3(config-if)#exit
S3(config)#
```

#### 2.4:	Отобразите данные протокола spanning-tree.

В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.

![](/Labworks/Lec-03_Lab03/img/pic6_Role_STS.jpg "Схема - роль и состояние портов")

__С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.__

**Вопрос:** Какой коммутатор является корневым мостом?  
**Ответ:** S1


**Вопрос:** Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?  
**Ответ:** Как имеющий наименьший MAC-адрес.


**Вопрос:** Какие порты на коммутаторе являются корневыми портами?  
**Ответ:**  На корневом коммутатор нет корневых (Root) портов, они все являются назначенными (DSG).
Корневыми портами являются порты имеющие наименьшую стоимость пути до корневого коммутатора.


**Вопрос:** Какие порты на коммутаторе являются назначенными портами?  
**Ответ:**  Назначенным портом выбирается тот, который имеет лучшую стоимость в данном сегменте.
У корневого коммутатора все порты назначенные.

**Вопрос:** Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?  
**Ответ:** Порт e0/1 на коммутаторе S3.

**Вопрос:** Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?  
**Ответ:** Потому что стоимость маршрута до корневого коммутатора через этот порт в этом сегменте сети выше. Данный порт назначен альтернативным - через него проходит запасной маршрут.


----
### 3: Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

#### 3.1:	Определите коммутатор с заблокированным портом

Выполните команду ```show spanning-tree``` на обоих коммутаторах *некорневого* моста.
В нашем случае это коммутаторы S2 и S3.

**коммутатор S2**
```
S2#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Root FWD 100       128.2    P2p
Et0/3               Desg FWD 100       128.4    P2p
```
![](/Labworks/Lec-03_Lab03/img/pic6_S2.jpg "S2#sh spanning-tree")

**коммутатор S3**
```
S3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        4 (Ethernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Altn BLK 100       128.2    P2p
Et0/3               Root FWD 100       128.4    P2p
```
![](/Labworks/Lec-03_Lab03/img/pic6_S3.jpg "S3#sh spanning-tree")

#### 3.2:	Измените стоимость порта.
Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, 
выделенный в качестве порта корневого моста. Уменьшите стоимость этого порта корневого моста до 18, 
выполнив команду spanning-tree cost 18 режима конфигурации интерфейса.
```
S3(config)#int e0/3
S3(config-if)#spanning-tree cost 18
```

#### 3.3:	Просмотрите изменения протокола spanning-tree.

**коммутатор S2**
```

S2#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Root FWD 100       128.2    P2p
Et0/3               Altn BLK 100       128.4    P2p
```

![](/Labworks/Lec-03_Lab03/img/pic7_S2.jpg "S2#sh spanning-tree")

**коммутатор S3**
```
S3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        18
             Port        4 (Ethernet0/3)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/1               Desg FWD 100       128.2    P2p
Et0/3               Root FWD 18        128.4    P2p
```

![](/Labworks/Lec-03_Lab03/img/pic7_S3.jpg "S3#sh spanning-tree")

**Вопрос:** Почему протокол spanning-tree заменяет ранее заблокированный порт на назначенный порт и блокирует порт, 
который был назначенным портом на другом коммутаторе?  
**Ответ:** Потому что изменилась стоимость порта (стоимость линков) к корневому коммутатору. Стоимость обратно пропорциональна пропускной способности. У ранее заблокированного порта стоимость стала ниже.
Соттветсвенно цена маршрута до корневого через ранее заблокированный порт стала ниже.

#### 3.4:	Удалите изменения стоимости порта.
a.	Выполните команду ```no spanning-tree cost 18``` режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.

b.	Повторно выполните команду ```show spanning-tree```, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, 
вернув исходные настройки порта. 


### 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
Необходимо активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

a.	Включите порты e0/0 и e0/2 на всех коммутаторах.
```
S1#(config)#int e0/0
S1#(config-if)#no shutdown
S1#(config-if)#
```
b.	Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду show spanning-tree на коммутаторах некорневого моста. 
Обратите внимание, что порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

**На коммутаторе S2**
```
S2#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        1 (Ethernet0/0)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.2000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Root FWD 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Desg FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
```

![](/Labworks/Lec-03_Lab03/img/pic8_S2.jpg "S2#sh spanning-tree")

**На коммутаторе S3**
```
S3#sh spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.1000
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.3000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Altn BLK 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Root FWD 100       128.3    P2p
Et0/3               Altn BLK 100       128.4    P2p
```
![](/Labworks/Lec-03_Lab03/img/pic8_S3.jpg "S3#sh spanning-tree")




**Вопрос:** Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?  
**Ответ:** S2 - Et0/0; S3 - Et0/2

**Вопрос:** Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?  
**Ответ:** Эти порты выбраны потому что их приоритет выше. При равных значения стоимости маршрута коорневым выбирается порт (интерфейс) с большим приоритетом. Наивысший приоритет имеет интерфейс с меньшим числом Prio.Nbr

|Коммутатор|Порт|Prio.Nbr|
|----------|-----------|----------|
|S2|e0/0|128.1|
|S2|e0/1|128.2|
|S3|e0/2|128.3|
|S3|e0/3|128.4|

----
### Вопросы для повторения
1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?  
	**О:** Первым используется значение стоимости маршрута от каждого своего порта до корневого коммутатора основанное на скорости своего порта.

2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?  
	**О:** Вторым будет использоваться наименьшее значение  BID (```Bridge ID Priority```) отправителя.

3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?  
	**О:** Третьим  значением при выборе порта является занчение приоритета (```Prio.Nbr```) своего порта



