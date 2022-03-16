# Настройка IPv6-адресов на сетевых устройствах 

**Задачи:**

  *Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора*
  
  *Часть 2. Ручная настройка IPv6-адресов*
  
  *Часть 3. Проверка сквозного соединения*
  
  **Топология:**
  
  ![image](https://user-images.githubusercontent.com/84719218/157433061-8a146392-e1e5-4334-8673-8bd65e04f8a8.png)


  **Таблица адресации**
  
| Устройство    | Интерфейс          | IPv6-адрес            | Длина префикса    | Шлюз по умолчанию |
| :-------------|:------------------ | :---------------------|:------------------|:------------------|
| R1            | G0/0/0             | 2001:db8:acad:а::1    | 64                |                   |
|               | G0/0/1             | 2001:db8:acad:1::1    | 64                |                   |
| S1            | VLAN 1             | 2001:db8:acad:1::b    | 64                |                   |
| PC-A          | NIC                | 2001:db8:acad:1::3    | 64                |fe80::1            |
| PC-B          | NIC                | 2001:db8:acad:а::3    | 64                |fe80::1            |

**Решение:**

Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

Шаг 1. Настроить маршрутизатор.

```
en
conf t
no ip domain-lookup
hostname R1
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
line vty 0 4
login
transport input ssh
end
```

```
conf t
enable secret class
```

Шаг 2. Настроить коммутатор.

```
conf t
sdm prefer dual-ipv4-and-ipv6 default 
reload
```

```
en
conf t
no ip domain-lookup
hostname S1
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
conf t
line vty 0 4
login
transport input ssh
end
```

```
conf t
enable secret class
```

Часть 2. Ручная настройка IPv6-адресов

Шаг 1. Назначить IPv6-адреса интерфейсам Ethernet на R1.

a) 
```
conf t
interface gigabitEthernet 0/0/0
ipv6 address 2001:db8:acad:a::1/64
no shutdown
exit
conf t
interface gigabitEthernet 0/0/1
ipv6 address 2001:db8:acad:1::1/64
no shutdown
exit
```

b)
```
show ipv6 interface brief
```

![image](https://user-images.githubusercontent.com/84719218/158586385-f1effa58-5cf9-4663-8c2f-ce40ce9f547c.png)

c)
```
conf t
interface gigabitEthernet 0/0/0
ipv6 add fe80::1 link-local
exit
interface gigabitEthernet 0/0/1
ipv6 add fe80::1 link-local
```

d)
```
show ipv6 interface brief
```

![image](https://user-images.githubusercontent.com/84719218/158585949-a6bf15bb-a1bc-4ae4-9a2d-2a227d981eda.png)


Шаг 2. Активировать IPv6-маршрутизацию на R1.

a)
```
ipconfig
```

![image](https://user-images.githubusercontent.com/84719218/158586634-52517784-1006-44bc-b4f2-39db9a2d6cb9.png)

b)
```
conf t
IPv6 unicast-routing
```

c)
```
ipconfig
```

![image](https://user-images.githubusercontent.com/84719218/158590994-3f8e5a54-0154-4df9-973c-feed09ffb286.png)

Шаг 3. Назначить IPv6-адреса интерфейсу управления (SVI) на S1.

a)
```
conf t
interface vlan1
ipv6 address 2001:db8:acad:1::b/64
no shutdown
exit
```
```
interface vlan1
ipv6 add fe80::1 link-local
```

b)
```
show ipv6 interface vlan 1
```
![image](https://user-images.githubusercontent.com/84719218/158593822-10369d71-de8f-44c9-82ec-8eabb84fd378.png)

Шаг 4. Назначить компьютерам статические IPv6-адреса.

![image](https://user-images.githubusercontent.com/84719218/158595435-81b7fead-fe63-438e-9a69-4bf6d23fcdc6.png)

![image](https://user-images.githubusercontent.com/84719218/158595502-6af5407a-310b-470a-be61-57476e4a4df8.png)

Часть 3. Проверка сквозного подключения

![image](https://user-images.githubusercontent.com/84719218/158595820-6f4185d7-41b2-438c-b75f-6e18dd267d1b.png)

![image](https://user-images.githubusercontent.com/84719218/158596524-4d228833-1c5b-4b3f-8bac-0daf5f38747d.png)

![image](https://user-images.githubusercontent.com/84719218/158596685-46c0c66c-d49e-4789-92bc-49ae8bde5f01.png)

![image](https://user-images.githubusercontent.com/84719218/158596916-f196e2f1-85ed-4fc3-92c3-cdd5bdacf402.png)







