# Настройка DHCPv6

**Топология**

![image](https://user-images.githubusercontent.com/84719218/164013933-b931523d-ae68-4b98-a19f-5a922410c0bc.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IPv6-адрес               |
| :-------------|:------------------ | :----------------------- |
| R1            | G0/0/0             | 2001:db8:acad:2::1/64    |
|               |                    | fe80::1                  |
|               | G0/0/1             | 2001:db8:acad:1::1/64    |
|               |                    | fe80::1                  |
| R2            | G0/0/0             | 2001:db8:acad:2::2/64    |
|               |                    | fe80::2                  |
|               | G0/0/1             | 2001:db8:acad:3::1/64    |
|               |                    | fe80::1                  |
| PC-A          | NIC                | DHCP                     |
| PC-B          | NIC                | DHCP                     |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Проверка назначения адреса SLAAC от R1*

*Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1*

*Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1*

*Часть 5. Настройка и проверка DHCPv6 Relay на R2*

**Решение:**

**Часть 1. Создать сеть и настроить основные параметры устройства**

Шаг 1. Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/164017335-c652f0c6-f49e-4d35-a09c-345d0fcfebcb.png)

Шаг 2. Настроить базовые параметры каждого коммутатора. 

```
en
conf t
no ip domain-lookup
hostname S1
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
```

```
int range f0/1-4
shutdown
int range f0/7-24
shutdown
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для коммутаторов S2)

Шаг 3. Произвести базовую настройку маршрутизаторов.

```
en
conf t
no ip domain-lookup
hostname S1
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
```

```
IPv6 unicast-routing
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для маршрутизатора R2)

Шаг 4. Настроить интерфейсы и маршрутизации для обоих маршрутизаторов.

a) 

```
conf t
int gig 0/0/0
ipv6 add 2001:db8:acad:2::1/64
ipv6 add fe80::1 link-local
no sh
ex
int gig 0/0/1
ipv6 add 2001:db8:acad:1::1/64
ipv6 add fe80::1 link-local
no sh
```

(Аналогично выполненна настройка для маршрутизатора R2)

b)
```
conf t
ipv6 route 2001:db8:acad:2::2/64 gigabitEthernet 0/0/0
copy running-config startup-config
```
(Аналогично выполненна настройка для маршрутизатора R2)

**Часть 2. Проверить назначение адреса SLAAC от R1**

```
ipconfig
```

![image](https://user-images.githubusercontent.com/84719218/164465636-3d99f070-80df-4fc8-b27d-867569b943f4.png)

**Часть 3. Настроить и проверить сервера DHCPv6 на R1**

Шаг 1. Более подробно изучить конфигурацию PC-A.

```
ipconfig /all
```

![image](https://user-images.githubusercontent.com/84719218/164467068-b515e1f1-5cbc-4a80-afb3-4b10f28b1479.png)

Шаг 2. Настройка R1 для предоставления DHCPv6 без состояния для PC-A.

```
conf t
ipv6 dhcp pool R1-STATELESS
dns-server 2001:db8:acad::254
domain-name STATELESS.com
```

```
ex
interface g0/0/1
ipv6 nd other-config-flag
ipv6 dhcp server R1-STATELESS
```

```
copy running-config startup-config
```

```
ipconfig /renew
ipconfig /all
```

![image](https://user-images.githubusercontent.com/84719218/164469063-ff20d90c-f7df-47b8-a43a-9491ac1c4ce8.png)

**Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1**

a)
```
conf t
ipv6 dhcp pool R2-STATEFUL
address prefix 2001:db8:acad:3:aaa::/80
dns-server 2001:db8:acad::254
domain-name STATEFUL.com
```

```
interface g0/0/0
ipv6 dhcp server R2-STATEFUL
```

**Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2.**

Шаг 1. Включить PC-B и проверить адрес SLAAC, который он генерирует.

```
ipconfig /all
```

![image](https://user-images.githubusercontent.com/84719218/164470741-7eecb8db-fb89-464d-8984-a86dc81accc5.png)

Шаг 2. Настроить R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.

```
conf t
int g0/0/1
ipv6 nd managed-config-flag
ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
```

```
copy running-config startup-config
```

Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B.

```
ipconfig /renew
ipconfig /all
```

![image](https://user-images.githubusercontent.com/84719218/164472618-b94baa8d-5d1c-4d88-84e9-f204dbe75011.png)



