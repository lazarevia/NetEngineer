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
![](/Labworks/Lec-03_Lab03/img/pic5_S1.jpg "выдючение портов на S1")

![](/Labworks/Lec-03_Lab03/img/pic5_S2.jpg "выдючение портов на S2")

![](/Labworks/Lec-03_Lab03/img/pic5_S3.jpg "выдючение портов на S3")

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

Какой коммутатор является корневым мостом?
Ответ: S1

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?
Ответ:
	Как имеющий наименьший MAC-адрес.

Какие порты на коммутаторе являются корневыми портами?
Ответ:

Какие порты на коммутаторе являются назначенными портами?
Ответ:

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?
Ответ:

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?
Ответ:


