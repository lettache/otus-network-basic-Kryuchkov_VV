# Реализация DHCPv4

**Топология**

![image](https://user-images.githubusercontent.com/84719218/165257844-856847e5-b9d5-4498-9467-3dbdb639d9a4.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 |Маска подсети    | Шлюз по умолчанию |
| :-------------|:------------------ | :----------------------- |:--------------- |:----------------- |
| R1            | G0/0/0             | 10.0.0.1                 | 255.255.255.252 | -                 |
|               | G0/0/1             | -                        | -               |                   |
|               | G0/0/1.100         | 192.168.1.1              | 255.255.255.192 |                   |
|               | G0/0/1.200         | 192.168.1.65             | 255.255.255.224 |                   |
|               | G0/0/1.1000        | -                        | -               |                   |
| R2            | G0/0/0             | 10.0.0.2                 | 255.255.255.252 | -                 |
|               | G0/0/1             | 192.168.1.97             | 255.255.255.240 |                   |
| S1            | VLAN 200           | 192.168.1.66             | 255.255.255.224 | 192.168.1.94      |
| S2            | VLAN 1             |                          |                 |                   |
| PC-A          | NIC                | DHCP                     | DHCP            | DHCP              |
| PC-B          | NIC                | DHCP                     | DHCP            | DHCP              |

**Таблица VLAN**

| VLAN          | Имя                | Назначенный интерфейс       |
| :-------------|:------------------ | :-------------------------- |
| 1             | -                  | S2: F0/18                   |
| 100           | Customers          | S1: F0/6                    |
| 200           | Management         | S1: VLAN 200                |
| 999           | Parking_Lot        | S1: F0/1-4, F0/7-24, G0/1-2 |
| 1000          | Own                | -                           |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Настройка и проверка двух серверов DHCPv4 на R1*

*Часть 3. Настройка и проверка DHCP-ретрансляции на R2*

**Решение:**

**Часть 1.	Создание сети и настройка основных параметров устройства**

Шаг 1.	Создать схему адресации

Шаг 2.	Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/165263557-62f5d355-0ef8-4013-ae40-3c9dede31060.png)

Шаг 3.	Произвести базовую настройку маршрутизаторов.


```
en
conf t
hostname R1
no ip domain-lookup
enable secret class
```

```
line console 0
logging synhronous
password cisco
login
end
```

```
conf t
line vty 0 4
enable secret cisco
```

```
service password-encryption
```

```
banner motd # Unauthorized access is strictly prohibited. #
ex
```

```
clock set 12:04:00 26 apr 2022
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для маршрутизатора R2)

Шаг 4.	Настроить маршрутизации между сетями VLAN на маршрутизаторе R1

a)

```
conf t
int g0/0/1
no sh
```

b)

```
int g0/0/1.100
encapsulation dot1Q 100
ip add 192.168.1.1 255.255.255.192
ex
```

```
int g0/0/1.200
encapsulation dot1Q 200
ip add 192.168.1.65 255.255.255.224
ex
```

```
int g0/0/1.1000
encapsulation dot1Q 1000 native
end
```

c)

```
sh int
```

Шаг 5.	Настроить G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a)

```
conf t
int g0/0/1
ip add 192.168.1.97 255.255.255.240
no sh
ex
```

b)

```
int g0/0/0
ip add 10.0.0.2 255.255.255.252
no sh
ex
```
(Аналогично выполненна настройка для маршрутизатора R1)

c)

```
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
(Аналогично выполненна настройка для маршрутизатора R1)

d)

```
ping 192.168.1.97
```

![image](https://user-images.githubusercontent.com/84719218/165310648-3bcd006c-9e01-431f-8e8b-229b47e287ce.png)

e)

```
copy running-config startup-config
```

Шаг 6.	Настроить базовые параметры каждого коммутатора.

```
en
conf t
hostname S1
no ip domain-lookup
enable secret class
```

```
line console 0
logging synhronous
password cisco
login
end
```

```
conf t
line vty 0 4
enable secret cisco
```

```
service password-encryption
```

```
banner motd # Unauthorized access is strictly prohibited. #
ex
```

```
clock set 16:35:00 26 apr 2022
```

```
copy running-config startup-config
```
(Аналогично выполненна настройка для коммутатора R2)

Шаг 7.	Создать сети VLAN на коммутаторе S1.


















