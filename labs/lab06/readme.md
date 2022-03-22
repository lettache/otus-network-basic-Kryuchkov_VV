# Внедрение маршрутизации между виртуальными локальными сетями

**Топология**

![image](https://user-images.githubusercontent.com/84719218/159425245-1d1e6f61-55f7-4b67-8e1e-740b54d83610.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес        |Маска подсети      |Шлюз по умолчанию |
| :-------------|:------------------ | :-------------- |:------------------|:-----------------|
| R1            | G0/0/1.10          | 192.168.10.1    | 255.255.255.0     |-                 |
|               | G0/0/1.20          | 192.168.20.1    | 255.255.255.0     |-                 |
|               | G0/0/1.30          | 192.168.30.1    | 255.255.255.0     |-                 |
|               | G0/0/1.1000        | -               | -                 |-                 |
| S1            | VLAN 10            | 192.168.10.11   | 255.255.255.0     | 192.168.10.1     |
| S2            | VLAN 10            | 192.168.10.12   | 255.255.255.0     | 192.168.10.1     |
| PC-A          | NIC                | 192.168.20.3    | 255.255.255.0     | 192.168.20.1     |
| PC-B          | NIC                | 192.168.30.3    | 255.255.255.0     | 192.168.30.1     |

**Таблица VLAN**

| VLAN          | Имя                | Назначенный интерфейс        |
| :-------------|:------------------ | :--------------------------- |
| 10            | Management         | S1: VLAN 10                  |
|               |                    | S2: VLAN 10                  |
| 20            | Sales              | S1: F0/6                     |
| 30            | Operations         | S2: F0/18                    |
| 999           | Parking_Lot        | С1: F0/2-4, F0/7-24, G0/1-2  |
|               |                    | С2: F0/2-17, F0/19-24, G0/1-2|
| 1000          | Native             | -                            |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Создание сетей VLAN и назначение портов коммутатора*

*Часть 3. Настройка транка 802.1Q между коммутаторами*

*Часть 4. Настройка маршрутизации между сетями VLAN*

*Часть 5. Проверка, что маршрутизация между VLAN работает*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/159429550-6d271547-e8f9-4a47-8aa9-cf80bcf4dd41.png)

Шаг 2. Настроить базовые параметры для маршрутизатора.

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
end
```

```
clock set 10:48:00 22 mar 2022
```

```
copy running-config startup-config
```

Шаг 3. Настроить базовые параметры каждого коммутатора.

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
end
```

```
clock set 11:05:00 22 mar 2022
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для второго коммутатора S2)

Шаг 4. Настройка узлов ПК.

![image](https://user-images.githubusercontent.com/84719218/159435774-a2304aa3-fc18-499b-9676-151c48fd0f84.png)

![image](https://user-images.githubusercontent.com/84719218/159435923-b896fcc5-4528-480c-b8a3-6b60c5d0960e.png)

**Часть 2. Создание сетей VLAN и назначение портов коммутатора**

Шаг 1. Создать сети VLAN на коммутаторах.

a)

```
conf t
vlan 10
name Management
no shutdown
exit
```

```
vlan 20
name Sales
no shutdown
exit
```

```
vlan 30
name Operations
no shutdown
exit
```

```
vlan 999
name Parking_Lot
no shutdown
exit
```

```
vlan 1000
name Native
no shutdown
exit
```
(Аналогичная настройка для второго коммутатора S2)

b)

```
int vlan 10
ip add 192.168.10.11 255.255.255.0
ip default-gateway 192.168.10.1
```

```
int vlan 10
ip add 192.168.10.12 255.255.255.0
ip default-gateway 192.168.10.1
```

c)

```
conf t
int range F0/2-4, F0/7-24, G0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
shutdown
```

```
conf t
int range F0/2-17, F0/19-24, G0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
shutdown
```

Шаг 2. Назначить сети VLAN соответствующим интерфейсам коммутатора.

a)

```
int f0/6
switchport mode access
switchport access vlan 20
ex
```

```
int f0/18
switchport mode access
switchport access vlan 30
ex
```

b)

```
end
show vlan
```

**Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами**

Шаг 1. Вручную настроить магистральный интерфейс F0/1 на коммутаторах S1 и S2.

a)

```
conf t
int fa0/1
switchport mode trunk
```

b)

```
switchport trunk native vlan 1000
switchport trunk allowed vlan 10,20,30,1000
end
```

c)

```
show int trunk
```
(Аналогичные комманды для другого коммутатора S2)




