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
int range f0/1-4
shutdown
int range f0/7-24
shutdown
```

```
copy running-config startup-config
```




