# Просмотр таблицы MAC-адресов коммутатора

**Задачи:**

  *1. Создание и настройка сети*
  
  *2. Изучение таблицы МАС-адресов коммутатора*
  
  **Топология:**
  
  ![изображение](https://user-images.githubusercontent.com/84719218/154082069-3b6f67ad-b404-472b-8a82-5f7d344bbff0.png)

  **Таблица адресации**
  
| Устройство    | Интерфейс          | IP-адрес        |Маска подсети      |
| :-------------|:------------------ | :-------------- |:------------------|
| S1            | VLAN 1             | 192.168.1.11    | 255.255.255.0     |
| S2            | VLAN 1             | 192.168.1.12    | 255.255.255.0     |
| PC-A          | NIC                | 192.168.1.1     | 255.255.255.0     |
| PC-B          | NIC                | 192.168.1.2     | 255.255.255.0     |

**Решение:**

**Часть 1. Создание и настройка сети**
--

**Шаг 1. Подключить сеть в соответствии с топологией**

![изображение](https://user-images.githubusercontent.com/84719218/154644969-923524ed-3ad4-4809-a906-6f97137470d8.png)

**Шаг 2. Настроить узлы ПК**

![изображение](https://user-images.githubusercontent.com/84719218/154645140-6fee2a87-f335-45d8-aaf0-7eb2409d2464.png)

![изображение](https://user-images.githubusercontent.com/84719218/154645168-3b876d0b-e047-4a99-a8ee-579be4e8e69a.png)

**Шаг 3. Выполнить инициализацию и перезагрузку коммутаторов**

```
en
show running-config
reload
```
(аналогично выполненно для второго коммутатора)

**Шаг 4. Настроить базовые параметры каждого коммутатора**

а) Настроить имена устройств в соответствии с топологией

```
en
conf t
hostname S1
```

б) Настроить IP-адреса, как указано в таблице адресации.

```
conf t
interface vlan 1
ip address 192.168.1.11 255.255.255.0
no shutdown
end
```

c)	Назначьте cisco в качестве паролей консоли и VTY.

```
line console 0
password cisco
login
end
```

```
conf t
line vty 0 4
enable secret cisco
end
```

d)	Назначить class в качестве пароля доступа к привилегированному режиму EXEC.

```
conf t
enable secret class
```
(аналогично выполненно для второго коммутатора)

**Часть 2. Изучение таблицы МАС-адресов коммутатора**
--

**Шаг 1. Записать МАС-адреса сетевых устройств**

а) 
```
ipconfig /all
```

![изображение](https://user-images.githubusercontent.com/84719218/154662654-88820467-517a-4c85-8d62-b5efe850f801.png)

![изображение](https://user-images.githubusercontent.com/84719218/154662761-c096cd79-1ea9-4da6-9da8-05f7e726a61f.png)

b)
```
en
show interface F0/1
```

![изображение](https://user-images.githubusercontent.com/84719218/154663560-e5a1cfba-45d6-4951-9a86-a3f90b9b2900.png)


![изображение](https://user-images.githubusercontent.com/84719218/154663444-801cbc49-9d8e-49bd-9b9c-1ae591c8ee5e.png)

**Шаг 2. Просмотреть таблицу МАС-адресов коммутатора**

a)
```
en
```

b)
```
show mac address-table
```

![изображение](https://user-images.githubusercontent.com/84719218/154667188-be12f99f-e6d6-41c1-b5d3-fc4e2542a7aa.png)

**Шаг 3. Очистить таблицу МАС-адресов коммутатора S2 и снова отобразить таблицу МАС-адресов**

а) 
```
en
clear mac address-table dynamic
```

b)
```
show mac address-table
```

![изображение](https://user-images.githubusercontent.com/84719218/154667658-99da8390-4d86-44d6-9131-8f60189b64ad.png)

**Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора**

a)
```
arp -a
```
![изображение](https://user-images.githubusercontent.com/84719218/154668150-0b62da07-15f6-45cd-8844-ba631280e2bf.png)

b)
```
ping 192.168.1.1
ping 192.168.1.11
ping 192.168.1.12
```
![изображение](https://user-images.githubusercontent.com/84719218/154668544-11698269-e3f2-4d59-b9d4-88b81cdc5751.png)

![изображение](https://user-images.githubusercontent.com/84719218/154668656-b99b59c9-449c-47ec-a98c-cd035ff4a05b.png)

![изображение](https://user-images.githubusercontent.com/84719218/154668758-366053b5-3f1d-43fd-84f9-f9b1944cfb28.png)

c)

```
show mac address-table
```

![изображение](https://user-images.githubusercontent.com/84719218/154669014-35a109cd-3edd-4603-9ac3-96e047b094d8.png)

```
arp -a
```

![изображение](https://user-images.githubusercontent.com/84719218/154669186-5d6f13c1-f9d8-42b9-bec1-cd7ccc329280.png)
