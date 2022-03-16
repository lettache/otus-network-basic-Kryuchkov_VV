# Доступ к сетевым устройствам по протоколу SSH

**Топология**

![image](https://user-images.githubusercontent.com/84719218/158599502-e680e4e0-734a-4579-8bef-ec243121d2e8.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес        |Маска подсети      |Шлюз по умолчанию |
| :-------------|:------------------ | :-------------- |:------------------|:-----------------|
| R1            | G0/0/1             | 192.168.1.1     | 255.255.255.0     |-                 |
| S1            | VLAN 1             | 192.168.1.11    | 255.255.255.0     | 192.168.1.1      |
| PC-A          | NIC                | 192.168.1.3     | 255.255.255.0     | 192.168.1.1      |

**Задачи**

*Часть 1. Настройка основных параметров устройства*

*Часть 2. Настройка маршрутизатора для доступа по протоколу SSH*

*Часть 3. Настройка коммутатора для доступа по протоколу SSH*

*Часть 4. SSH через интерфейс командной строки (CLI) коммутатора*

**Решение:**

**Часть 1. Настроить основные параметры устройств**

Шаг 1. Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/158602705-dd685c8f-1c35-4aaa-a654-ab047a5f5495.png)

Шаг 2. Выполнить инициализацию и перезагрузку маршрутизатора и коммутатора.

```
reload
```

Шаг 3. Настроить маршрутизатор.

**2. Настройка базовых параметров сетевых устройств**

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
end
```

```
conf t
service password-encryption
```

```
banner motd # Unauthorized access is strictly prohibited. #
```
  
```
conf t
interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
end
conf t
ip default-getaway 192.168.1.1
```

```
conf t
interface gigabitEthernet 0/0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
```

```
copy running-config startup-config
```





