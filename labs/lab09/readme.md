# Конфигурация безопасности коммутатора

**Топология**

![image](https://user-images.githubusercontent.com/84719218/165506807-bc3e5844-81d6-4542-9abc-e364d4fbd51d.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 |Маска подсети    |
| :-------------|:------------------ | :----------------------- |:--------------- |
| R1            | G0/0/1             | 192.168.10.1             | 255.255.255.0   |
|               | Loopback 0         | 10.10.1.1                | 255.255.255.0   |
| S1            | VLAN 10            | 192.168.10.201           | 255.255.255.0   |
| S2            | VLAN 10            | 192.168.10.202           | 255.255.255.0   |
| PC-A          | NIC                | DHCP                     | 255.255.255.0   |
| PC-B          | NIC                | DHCP                     | 255.255.255.0   |

**Задачи**

*Часть 1. Настройка основного сетевого устройства*

*Часть 2. Настройка сетей VLAN*

*Часть 3: Настройки безопасности коммутатора*

**Решение:**

**Часть 1. Настройка основного сетевого устройства**

Шаг 1. Создать сеть.

![image](https://user-images.githubusercontent.com/84719218/165507953-07c227a8-e3e7-4641-b248-4f0a8521e6d5.png)

Шаг 2. Настроить маршрутизатор R1

```
show ip interface brief
```

![image](https://user-images.githubusercontent.com/84719218/165509741-6ced7569-e973-40c8-9781-ffe90bdf1dcb.png)

Шаг 3. Настройка и проверка основных параметров коммутатора

```
en
conf t
no ip domain-lookup
hostname S1
```

```
int f0/5
description Router R1
ex
int f0/6
description PC-A
ex
int f0/1
description Switch S2
ex
```

```
int vlan 1
ip address 192.168.10.1 255.255.255.0
no sh
end
```
(Аналогично выполненна настройка для коммутатора S2)

**Часть 2. Настройка сетей VLAN на коммутаторах.**

Шаг 1. Сконфигурировать VLAN 10.

```
conf t
vlan 10
name Management
```
(Аналогично выполненна настройка для коммутатора S2)

Шаг 2. Сконфигурировать SVI для VLAN 10.

```
conf t
int vlan 10
ip add 192.168.10.201 255.255.255.0
no sh
des Management
ex
```
(Аналогично выполненна настройка для коммутатора S2)

![image](https://user-images.githubusercontent.com/84719218/165916018-ebeeec19-6c42-48e0-b2df-5b11de01818b.png)

![image](https://user-images.githubusercontent.com/84719218/165916484-d02ce535-4095-4d02-ad5c-d2bc25f1e408.png)


Шаг 3. Настроить VLAN 333 с именем Native на S1 и S2.

```
conf t
vlan 333
name Native
```
(Аналогично выполненна настройка для коммутатора S2)

Шаг 4. Настроить VLAN 999 с именем ParkingLot на S1 и S2.

```
conf t
vlan 999
name ParkingLot
```
(Аналогично выполненна настройка для коммутатора S2)

```
sh vl
```

![image](https://user-images.githubusercontent.com/84719218/165915498-5a72557c-0a1c-4e3a-9f64-5b14c559211a.png)


**Часть 3. Настроить безопасности коммутатора.**

Шаг 1. Релизация магистральных соединений 802.1Q.

```
conf t
int f0/1
switchport mode trunk
switchport trunk native vlan 333
switchport trunk allowed vlan 1,10,333,999
ex
```
(Аналогично выполненна настройка для коммутатора S2)

```
show interface trunk
```

```
conf t
int f0/1
switchport nonegotiate
```

```
show interface int f0/1
```
(Аналогично выполненна настройка для коммутатора S2)

Шаг 2. Настройка портов доступа

a)

```
conf t
int range f0/5-6
switchport mode access
switchport access vlan 10
ex
```

b)

```
conf t
int f0/18
switchport mode access
switchport access vlan 10
ex
```

Шаг 3. Безопасность неиспользуемых портов коммутатора

```
conf t
int range f0/2-4, f0/7-24, g0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
sh
ex
```

```
conf t
int range f0/2-17, f0/19-24, g0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
sh
ex
```

```
show interfaces status
```

Шаг 4. Документирование и реализация функций безопасности порта.

a)

```
show port-security interface f0/6
```

![image](https://user-images.githubusercontent.com/84719218/165937104-4725ec5a-8a2c-42ff-aca4-bfcc601727b3.png)


|              Конфигурация безопасности порта по умолчанию             |
| :-------------------------------------------------------------------- | 

| Функция                                    | Настройка по умолчанию   | 
| :------------------------------------------|:------------------------ | 
| Защита портов                              | Disabled                 |
| Максимальное количество записей MAC-адресов| 1                        |
| Режим проверки на нарушение безопасности   | Shutdown                 |
| Aging Time                                 | 0 mins                   |
| Aging Type                                 | Absolute                 |
| Secure Static Address Aging                | Disabled                 |
| Sticky MAC Address                         | 0                        |

b)

```
conf t
int f0/6
switchport port-security
switchport port-security maximum 3
switchport port-security violation restrict
switchport port-security aging time 60
switchport port-security aging-type inactivity
```

c)

```
show port-security interface f0/6
```

![image](https://user-images.githubusercontent.com/84719218/165937162-259432b9-b80b-4574-a2cf-eb8e3b0367cd.png)

```
show port-security address
```

![image](https://user-images.githubusercontent.com/84719218/165937308-26277131-316c-4556-88f9-d236945bc929.png)

d)

```
conf t
int f0/18
switchport port-security
```

e)

```
switchport port-security maximum 2
switchport port-security violation protect
switchport port-security aging time 60
end
```

f)

```
show port-security interface f0/18
```

![image](https://user-images.githubusercontent.com/84719218/165937835-4b4dcc80-cf76-42e6-a9e4-1a2306b716c5.png)

```
show port-security address
```

![image](https://user-images.githubusercontent.com/84719218/165937929-9df77fb6-f20a-4acc-b399-112a14e18283.png)

Шаг 5. Реализовать безопасность DHCP snooping.

a)
```
en
conf t
ip dhcp-snooping
ip dhcp-snooping vlan 10
```

b)
```
int f0/1
ip dhcp-snooping trust
```

c)
```
int f0/18
ip dhcp-snooping limit rate 5
```

d)
```
show ip dhcp snooping
```

![image](https://user-images.githubusercontent.com/84719218/165951739-039d167e-6d77-429d-a1f4-e03ec8d9603b.png)

e)
```
ipconfig /release
ipconfig /renew
```

f)
```
show ip dhcp snooping binding 
```

![image](https://user-images.githubusercontent.com/84719218/165951960-b7ed8b43-f707-4445-b87b-d6a430895558.png)

Шаг 6. Реализация PortFast и BPDU Guard

a)
```
conf t
int range f0/1, f0/5
spanning-tree portfast
```

```
conf t int f0/1
spanning-tree portfast
```

b)
```
int f0/6
spanning-tree bpduguard enable
ex
```

```
int f0/18
spanning-tree bpduguard enable
end
```

c)
```
show spanning-tree interface f0/6 detail
```

![image](https://user-images.githubusercontent.com/84719218/165952241-edd9e009-9856-418a-aeb1-cd01769ed74a.png)









