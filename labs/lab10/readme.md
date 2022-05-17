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





















