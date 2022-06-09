# Настройка NAT для IPv4

**Топология**

![image](https://user-images.githubusercontent.com/84719218/172834664-09c854df-8c77-4616-af5f-be56c1915278.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 | Маска подсети   |
| :-------------|:------------------ | :----------------------- |:--------------- |
| R1            | G0/0/0             | 209.165.200.230          | 255.255.255.248 |
|               | G0/0/1             | 192.168.1.1              | 255.255.255.0   |
| R2            | G0/0/0             | 209.165.200.225          | 255.255.255.248 |
|               | Lo1                | 209.165.200.1            | 255.255.255.224 |
| S1            | VLAN 1             | 192.168.1.11             | 255.255.255.0   |
| S2            | VLAN 1             | 192.168.1.12             | 255.255.255.0   |
| PC-A          | NIC                | 192.168.1.2              | 255.255.255.0   |
| PC-B          | NIC                | 192.168.1.3              | 255.255.255.0   |


**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Настройка и проверка NAT для IPv4*

*Часть 3. Настройка и проверка PAT для IPv4*

*Часть 4. Настройка и проверка статического NAT для IPv4*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Подключить кабеля сети согласно приведенной топологии

![image](https://user-images.githubusercontent.com/84719218/172836445-91ec3676-676e-405d-bc43-5040a2e0791c.png)

Шаг 2. Произведите базовую настройку маршрутизаторов

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
```

```
int g0/0/0
ip add 209.165.200.230 255.255.255.248
no sh
ex
int g0/0/1
ip add 192.168.1.1 255.255.255.0
no sh
ex
```

```
ip route 0.0.0.0 0.0.0.0 g0/0/0
```

```
copy running-config startup-config
```
(Аналогично выполненна настройка для маршрутизатора R2)

Шаг 3. Настройте базовые параметры каждого коммутатора.

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
```

```
int range F0/2-17, F0/19-24
sh
ex
```

```
int vlan 1
ip add 192.168.1.12 255.255.255.0
no sh
ex
```

```
conf t
int range F0/2-4, f0/7-24
sh
ex
```

```
int vlan 1
ip add 192.168.1.11 255.255.255.0
no sh
ex
```

```
copy running-config startup-config
```
(Аналогично выполненна настройка для коммутатора S2)






















































