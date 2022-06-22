# Настройка протокола OSPFv2 для одной области

**Топология**

![image](https://user-images.githubusercontent.com/84719218/165545719-3348afe0-d231-438e-a9fb-a7d5b9d7379c.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 |Маска подсети    |
| :-------------|:------------------ | :----------------------- |:--------------- |
| R1            | G0/0/1             | 10.53.0.1                | 255.255.255.0   |
|               | Loopback1          | 172.16.1.1               | 255.255.255.0   |
| R2            | G0/0/1             | 10.53.0.2                | 255.255.255.0   |
|               | Loopback1          | 192.168.1.1              | 255.255.255.0   |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области*

*Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/165689538-911dd572-482d-472b-a87d-44e803c82d63.png)

Шаг 2. Произвести базовую настройку маршрутизаторов.

```
en
conf t
no ip domain-lookup
hostname R1
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
login
```

```
service password-encryption
```

```
banner motd # Unauthorized access is strictly prohibited. #
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для маршрутизатора R2)

Шаг 3. Настроить базовые параметры каждого коммутатора.

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
login
```

```
service password-encryption
```

```
banner motd # Unauthorized access is strictly prohibited. #
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для коммутатора S2)

**Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области**

Шаг 1. Настроить адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.

```
conf t
int g0/0/1
ip add 10.53.0.1 255.255.255.0
ex
int loop 1
ip add 172.16.1.1 255.255.255.0
ex
```
(Аналогично выполненна настройка для маршрутизатора R2)

```
conf t
router ospf 56
no passive-interface g0/0/1
```

```
router-id 1.1.1.1
```
(Аналогично выполненна настройка для маршрутизатора R2)

```
network 10.53.0.0 0.0.0.255 area 0
```
(Аналогично выполненна настройка для маршрутизатора R2)

```
network 172.16.1.0 0.0.0.255 area 0
end
```

```
sh ip ospf int
```

![изображение](https://user-images.githubusercontent.com/84719218/170275805-dc115341-9153-4f23-b735-8bfdacfb920e.png)

```
show ip route ospf
```

![изображение](https://user-images.githubusercontent.com/84719218/174987840-d7368c49-76dd-4399-98a0-d12e9275e42a.png)

```
ping 192.168.1.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174988067-d710ab53-137c-4f15-884e-91ab5ebbb35c.png)

**Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области**

Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.

```
conf t
int g0/0/1
ip ospf priority 50
```

```
ip ospf hello-interval 30
ip ospf dead-interval 120
```
(Аналогично выполненна настройка для маршрутизатора R2)

```
ip route 0.0.0.0 0.0.0.0 loopback 1
```

```
conf t
router ospf 56
auto-cost reference-bandwidth 10000
end
clear ip ospf process
```
(Аналогично выполненна настройка для маршрутизатора R2)

```
conf t
rout ospf 56
network 192.168.1.1 0.0.0.255 area 0
end
```

```
conf t
rout ospf 56
passive-interface loopback 1
```

```
clear ip ospf process
```

Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.

```
show ip ospf interface g0/0/1
```

![изображение](https://user-images.githubusercontent.com/84719218/174988920-5894cc9a-8a06-476e-b1d2-3a4315a6682c.png)

```
show ip route ospf
```

![изображение](https://user-images.githubusercontent.com/84719218/174989085-d1e6a8c2-2c24-4742-b41e-e26e7eb709bd.png)

```
ping 172.16.1.1
```

![изображение](https://user-images.githubusercontent.com/84719218/170653421-5592615e-f5cb-4d63-9d74-d31c792a8753.png)




