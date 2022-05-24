# Настройка и проверка расширенных списков контроля доступа

**Топология**

![изображение](https://user-images.githubusercontent.com/84719218/169963837-2e59df8f-6b78-4737-b84d-5fab3fd0b150.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 | Маска подсети   | Шлюз по умолчанию |
| :-------------|:------------------ | :----------------------- |:--------------- | :---------------- |
| R1            | G0/0/1             | -                        | -               | -                 |
|               | G0/0/1.20          | 10.20.0.1                | 255.255.255.0   |                   |
|               | G0/0/1.30          | 10.30.0.1                | 255.255.255.0   |                   |
|               | G0/0/1.40          | 10.40.0.1                | 255.255.255.0   |                   |
|               | G0/0/1.1000        | -                        | -               |                   |
|               | Loopback1          | 172.16.1.1               | 255.255.255.0   |                   |
| R2            | G0/0/1             | 10.20.0.4                | 255.255.255.0   |                   |
| S1            | VLAN 20            | 10.20.0.2                | 255.255.255.0   | 10.20.0.1         |
| S2            | VLAN 20            | 10.20.0.3                | 255.255.255.0   | 10.20.0.1         |
| PC-A          | NIC                | 10.30.0.10               | 255.255.255.0   | 10.30.0.1         |
| PC-B          | NIC                | 10.40.0.10               | 255.255.255.0   | 10.40.0.1         |

**Таблица VLAN**

| VLAN          | Имя                | Назначенный интерфейс                 |
| :-------------|:------------------ | :------------------------------------ |
| 20            | Management         | S2: F0/5                              |
| 30            | Operations         | S1: F0/6                              |
| 40            | Sales              | S2: F0/18                             |
| 999           | Parking_Lot        | S1: F0/2-4, F0/7-24, G0/1-2           |
|               |                    | S2: F0/2-4, F0/6-17, F0/19-24, G0/1-2 |
| 1000          | Native             | -                                     |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Настройка и проверка списков расширенного контроля доступа*

**Решение:**

**Часть 1.	Создание сети и настройка основных параметров устройства**

Шаг 1. Создать сеть согласно топологии.

![изображение](https://user-images.githubusercontent.com/84719218/169966805-a2d3cfd3-e3ce-4c21-b08c-53467040be5b.png)

Шаг 2. Произвести базовую настройку маршрутизаторов.

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
copy running-config startup-config
```

(Аналогично выполненна настройка для маршрутизатора R2)

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
ex
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для коммутатора S2)

**Часть 2. Настройка сетей VLAN на коммутаторах.**

Шаг 1. Создать сети VLAN на коммутаторах.

a)

```
conf t
vlan 20
name Management
ex
```

```
vlan 30
name Operations
ex
```

```
vlan 40
name Sales
ex
```

```
vlan 999
name ParkingLot
ex
```

```
vlan 1000
name Native
ex
```

(Аналогично выполненна настройка для коммутатора S2)

b)

```
int vlan 20
ip add 10.20.0.2 255.255.255.0
ip default-gateway 10.20.0.1
```
 
```
int vlan 20
ip add 10.20.0.3 255.255.255.0
ip default-gateway 10.20.0.1
```

c)

```
conf t
int range F0/2-4, F0/7-24, g0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
sh
```

```
conf t
int range F0/2-4, F0/6-17, f0/19-24, g0/1-2
switchport mode access
switchport access vlan 999
ex
int vlan 999
sh
```

Шаг 2. Назначить сети VLAN соответствующим интерфейсам коммутатора.

a)

```
int f0/5
switchport mode access
switchport access vlan 20
ex
```

```
int f0/6
switchport mode access
switchport access vlan 30
ex
```

```
int f0/18
switchport mode access
switchport access vlan 40
end
```

b)

```
show vlan brief
```

![изображение](https://user-images.githubusercontent.com/84719218/169973815-a37e907d-0a9d-49cf-9bee-b85dd77644ad.png)

![изображение](https://user-images.githubusercontent.com/84719218/169973913-7b4e4ecf-cce2-4d3a-a64d-202286333cf8.png)

**Часть 3. ·Настроить транки (магистральные каналы).**

Шаг 1. Вручную настроить магистральный интерфейс F0/1.

a)

```
conf t
int f0/1
switchport mode trunk
```

b)

```
switchport trunk native vlan 1000
```

c)

```
switchport trunk allowed vlan 20,30,40,999,1000
end
```
(Аналогично выполненна настройка для коммутатора S2)

d)

```
sh int trunk
```

![изображение](https://user-images.githubusercontent.com/84719218/169978469-8fa90442-efe1-41e4-86d9-e8dea4f2ee72.png)

![изображение](https://user-images.githubusercontent.com/84719218/169978568-0d897849-0229-43d7-ad3f-78e9b5556552.png)








































