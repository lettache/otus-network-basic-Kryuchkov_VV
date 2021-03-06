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

Шаг 2. Вручную настроить магистральный интерфейс F0/5 на коммутаторе S1.

a)

```
conf t
int f0/5
switchport mode trunk
```

```
switchport trunk native vlan 1000
```

```
switchport trunk allowed vlan 20,30,40,999,1000
end
```

b)

```
copy running-config startup-config
```

c)

```
sh int trunk
```

![изображение](https://user-images.githubusercontent.com/84719218/169981077-9c374eeb-29e4-485d-962a-b462e82eaa2a.png)

**Часть 4. Настройте маршрутизацию.**

Шаг 1. Настроить маршрутизации между сетями VLAN на R1.

a)

```
conf t
int g0/0/1
no sh
ex
```

b)

```
int g0/0/1.20
encapsulation dot1Q 20
ip add 10.20.0.1 255.255.255.0
ex
```

```
int g0/0/1.30
encapsulation dot1Q 30
ip add 10.30.0.1 255.255.255.0
ex
```

```
int g0/0/1.40
encapsulation dot1Q 40
ip add 10.40.0.1 255.255.255.0
ex
```

```
int g0/0/1.1000
encapsulation dot1Q 1000 native
end
```

```
sh int g0/0/1.1000
```

![изображение](https://user-images.githubusercontent.com/84719218/169983565-12f28fb4-24da-4c5b-8daa-972cb06b6b1e.png)

c)

```
conf t
int loop 1
ip add 172.16.1.1 255.255.255.0
end
```

d)


```
show ip interface brief
```

![изображение](https://user-images.githubusercontent.com/84719218/169983850-e89670ab-6a2b-4e11-a5b1-a5b8b83e7951.png)

Шаг 2. Настроить интерфейс R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1

```
conf t
int g0/0/1
ip add 10.20.0.4 255.255.255.0
no sh
ex
```

```
ip route 0.0.0.0 0.0.0.0 10.20.0.1
```

**Часть 5. Настройте удаленный доступ**

Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

a)

```
conf t
username SSHadmin privilege 15 password $cisco123!
```

b)

```
ip domain-name ccna-lab.com 
```

c)

```
crypto key generate rsa
1024
```

d)

```
line vty 0 5
transport input ssh
login local
end
```
(Аналогично выполненна настройка для остальных сетевых устройств)

Шаг 2. Включить защищенные веб-службы с проверкой подлинности на R1.

a)

```
conf t
ip http secure-server 
```

b)

```
ip http authentication local
```

**Часть 6. Проверка подключения**

Шаг 1. Настройка узлов ПК.

![изображение](https://user-images.githubusercontent.com/84719218/169989500-0616d345-e968-4cdd-b4e0-556562b8f6c7.png)

![изображение](https://user-images.githubusercontent.com/84719218/169989793-1abc3546-3f54-44a4-895d-da586faeec49.png)

Шаг 2. Выполнить следующие тесты. Эхозапрос должен пройти успешно.

| От            | Протокол           | Назначение   |
| :-------------|:------------------ | :----------- |
| PC-A          | Ping               | 10.40.0.10   |
| PC-A          | Ping               | 10.20.0.1    |
| PC-B          | Ping               | 10.30.0.10   |
| PC-B          | Ping               | 10.20.0.1    |
| PC-B          | Ping               | 172.16.1.1   |
| PC-B          | HTTPS              | 10.20.0.1    |
| PC-B          | HTTPS              | 172.16.1.1   |
| PC-B          | SSH                | 10.20.0.1    |
| PC-B          | SSH                | 172.16.1.1   |

![изображение](https://user-images.githubusercontent.com/84719218/169991755-91444008-20c1-417b-8f21-a1c9e78f4bec.png)

![изображение](https://user-images.githubusercontent.com/84719218/169991881-85005b26-ae52-47aa-a6b2-f54ff05f1b88.png)

![изображение](https://user-images.githubusercontent.com/84719218/169992054-02a3dc60-0c73-4a8d-86c4-34a7e1cebe22.png)

![изображение](https://user-images.githubusercontent.com/84719218/169992150-268b0d56-af11-43e1-93db-5289d3efe472.png)

![изображение](https://user-images.githubusercontent.com/84719218/169992237-2f8b6310-5111-4df0-b768-853df9513f4b.png)

![изображение](https://user-images.githubusercontent.com/84719218/170008869-ab3c7f70-a6e1-4b41-a18e-0398f5338359.png)

![изображение](https://user-images.githubusercontent.com/84719218/170008953-f928b769-e244-43bc-b67a-4f568293d8ee.png)

![изображение](https://user-images.githubusercontent.com/84719218/170009053-4dd4176a-f0c6-4b2e-ae13-3a62d01db79e.png)

![изображение](https://user-images.githubusercontent.com/84719218/170009119-18b161d7-9823-4c74-99b0-21f04cfd13ff.png)

**Часть 7. Настройка и проверка списков контроля доступа (ACL)**

Шаг 1. Проанализировать требования к сети и политике безопасности для планирования реализации ACL.

![изображение](https://user-images.githubusercontent.com/84719218/175299828-30393d4d-29af-4234-8fe7-c7ba815cba6a.png)

Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.

```
conf t
ip access-list extended 130
deny icmp any 10.40.0.0 0.0.0.255
permit any any
ex
ip access-list extended 140
deny tcp any 10.20.0.0 0.0.0.255 eq 22
deny tcp any 10.20.0.0 0.0.0.255 eq 80
deny tcp any 10.20.0.0 0.0.0.255 eq 443
deny tcp any host 10.30.0.1 eq 80
deny tcp any host 10.30.0.1 eq 443
deny tcp any host 10.40.0.1 eq 80
deny tcp any host 10.40.0.1 eq 443
deny icmp any 10.20.0.0 0.0.0.255
deny icmp any 10.30.0.0 0.0.0.255
permit ip any any
ex
int g0/0/1.30
ip access-group 130 in
ex
int g0/0/1.40
ip access-group 140 in
ex
```

Шаг 3. Убедиться, что политики безопасности применяются развернутыми списками доступа.

![изображение](https://user-images.githubusercontent.com/84719218/175301042-db1df869-e074-4268-9b8e-65dfa5d11cce.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301151-3c15cbb3-78ba-4412-a7b4-e2704fd61505.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301287-a85c7014-8239-4d0f-b316-03d4fba49d83.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301445-0a1beb78-4b57-48f8-bf5c-a139335bebac.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301543-f2735fb7-5f16-4004-8b8e-552c0b2a3937.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301680-9186c00e-60df-4bba-888b-7d77cbdf3493.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301811-3e9ecce2-49ad-4e58-82b3-890bc5800fd4.png)

![изображение](https://user-images.githubusercontent.com/84719218/175301914-c2b651fc-97d0-4bb7-be7d-f0e8107e7029.png)

![изображение](https://user-images.githubusercontent.com/84719218/175302007-7e40187b-9b3d-43be-9221-bdaa6baafb2e.png)



































