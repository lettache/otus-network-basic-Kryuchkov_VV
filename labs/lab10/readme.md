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

```
ping 192.168.1.1
```

![изображение](https://user-images.githubusercontent.com/84719218/170278276-e3df4deb-a14a-446d-8081-f46ecd5fc9c9.png)

**Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области**

Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.

```
conf t
int g0/0/1
ip ospf priority 50
```

```
ip ospf hello-interval 30
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
passive-interface gigabitEthernet 0/0/1
```

```
clear ip ospf process
```

Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.

```
show ip ospf interface g0/0/1
```

![изображение](https://user-images.githubusercontent.com/84719218/170653004-af721db0-7697-4e2d-ad9e-cd09ac6b2cf8.png)

```
show ip route ospf
```

![изображение](https://user-images.githubusercontent.com/84719218/170653271-f6c0be14-534b-40e3-88e8-346e5ab8411d.png)

```
show ip route ospf
```

![изображение](https://user-images.githubusercontent.com/84719218/170653233-5a330425-5f75-4c80-949d-16884aef37a9.png)

```
ping 172.16.1.1
```

![изображение](https://user-images.githubusercontent.com/84719218/170653421-5592615e-f5cb-4d63-9d74-d31c792a8753.png)




