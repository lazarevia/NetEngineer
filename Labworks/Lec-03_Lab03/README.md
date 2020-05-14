# Развертывание коммутируемой сети с резервными каналами
	Топология
 ![](/Labworks/Lec-03_Lab03/img/scheme.jpg "Топология")

Таблица адресации
	
|Устройство		|Интерфейс	|IP-адрес	|Маска подсети	|
|:--------------|:--------------|:--------------|:------------------|
|S1		|VLAN 1		|192.168.1.1	|255.255.255.0	|
|S2		|VLAN 1		|192.168.1.2	|255.255.255.0	|
|S3		|VLAN 1		|192.168.1.3	|255.255.255.0	|


## Задачи
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

Соответсвие портов задачи и стенда (схемы)

|Порт задачи|Порт решения|
|----------|-----------|
|F0/1|e0/0|
|F0/2|e0/1|
|F0/3|e0/2|
|F0/4|e0/3|


----
### 1:	Создание сети и настройка основных параметров устройства
В части 1 вам предстоит настроить топологию сети и основные параметры маршрутизаторов.
#### 1.1:	Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### 1.2:	Выполните инициализацию и перезагрузку коммутаторов.

#### 1.3:	Настройте базовые параметры каждого коммутатора.
a.	Отключите поиск DNS.
```
Switch(config)# no ip domain-lookup
Switch(config)#
```
b.	Присвойте имена устройствам в соответствии с топологией.
```
Switch(config)# hostname S1
S1(config)#
```
c.	Назначьте ___class___ в качестве зашифрованного пароля доступа к привилегированному режиму.
```
S1(config)# service password-encryption
S1(config)#
S1(config)# enable secret class
S1(config)#
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

#### 1.4:	Проверьте связь.

Проверьте способность компьютеров обмениваться эхо-запросами.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?	
Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?

Ответ:
Успешно
![](/Labworks/Lec-03_Lab03/img/pic4_S1.jpg "")

Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?

Ответ:
Успешно
![](/Labworks/Lec-03_Lab03/img/pic4_S2.jpg "")

Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

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

### Часть 3: Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

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



