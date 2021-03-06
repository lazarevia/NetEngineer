#  Архитектура сети. Адресное прострнство. IPv4/6

Топология проекта
 ![](/Labworks/Lab_04/Project_Network_EVENG.PNG "Топология проекта")

## Цель: 
В данной самостоятельной работе необходимо распланировать адресное пространство
Настроить IP на всех активных портах для дальнейшей работы над проектом
Адресное пространство должно быть задокументировано

## Задачи 

1. разработать и задокументировать адресное пространство для лабораторного стенда.
2. настроить ip адреса на каждом активном порту
4. настроить каждый VPC в каждом офисе в своем VLAN.
5. настроить VLAN управления для сетевых устройств
6. настроить сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано.
7. использовать ipv4 и ipv6

## Решение

### 1. разработать и задокументировать адресное пространство для лабораторного стенда.

Таблица адресации

|  IPv4 |Summ Net  |IPv6   |Summ  Net |  Desc | Eq&port  |
| :------------ | :------------ | :------------ | :------------ | :------------ | :------------ |
|   |10.10.0.0/23 |   |   | Киторн  |   |
|   |   |   |   |   |   |
|   |30.20.0.0/23 |   |   | Ламас  |   |
|   |   |   |   |   |   |
|   |14.20.0/23 |   |   | Чокурдах  |   |
|   |   |   |   |   |   |
|   |77.20.0.0/18 |   |   | Москва  |   |
|   |   |   |   |   |   |
|   |98.20.0/20   |   |   | С-Питер  |   |
|   |   |   |   |   |   |
|Триада|
|33.33.0.0.24   |33.33.0.0/22|   |   |Триада   |R23e0/2 R24e0/2   |
|33.33.1.0/24   |33.33.0.0/22|   |   |Триада   |R23e0/1 R25e0/0   |
|33.33.2.0/24   |33.33.0.0/22|   |   |Триада   |R25e0/3 R26e0/0   |
|33.33.3.0/24   |33.33.0.0/22|   |   |Триада   |R24e0/1 R26 e0/0  |




Итоговая схема с IP адресами

 ![](/Labworks/Lab_04/Project_Network_IP.png "Топология проекта")
 
 
 использовалось:
 
 
 https://markdown-editor.github.io/
