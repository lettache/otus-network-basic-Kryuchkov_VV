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

```
en
conf t
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
interface gigabitEthernet 0/0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
end
```

```
copy running-config startup-config
```

Шаг 4. Настройка компьютер PC-A.

![image](https://user-images.githubusercontent.com/84719218/158607709-f666eb4e-2a58-4c5b-a685-d7410090fb83.png)

Шаг 5. Проверить подключение к сети.

![image](https://user-images.githubusercontent.com/84719218/158608029-c1d369fa-a7f5-45a7-a8b7-e2c6dc00e0ae.png)

**Часть 2. Настроить маршрутизатор для доступа по протоколу SSH**

Шаг 1. Настроить аутентификацию устройств.

```
conf t
hostname R1
ip domain-name example.com
```

Шаг 2. Создать ключ шифрования с указанием его длины.

```
crypto key generate rsa
2048
```

Шаг 3. Создать имя пользователя в локальной базе учетных записей.

```
conf t
username admin privilege 15 password Adm1nP@55
```

Шаг 4. Активировать протокол SSH на линиях VTY.

a)
```
line vty 0 4
transport input ssh
transport innput telnet
```

b)
```
login local
```

Шаг 5. Сохранить текущую конфигурацию в файл загрузочной конфигурации.

```
copy running-config startup-config
```

Шаг 6. Установить соединение с маршрутизатором по протоколу SSH.

![image](https://user-images.githubusercontent.com/84719218/158615633-3b2eb75b-001d-4c7f-8cb9-f145ed1d559d.png)

**Часть 3. Настройка коммутатора для доступа по протоколу SSH**

Шаг 1. Настройка основных параметры коммутатора.

```
en
conf t
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
interface vlan 1
ip address 192.168.1.11 255.255.255.0
no shutdown
end
```

```
copy running-config startup-config
```

Шаг 2. Настройте коммутатор для соединения по протоколу SSH.

a)
```
conf t
hostname S1
```

b)
```
ip domain-name example.com
```

c)
```
crypto key generate rsa
2048
```

d)
```
conf t
username admin privilege 15 password Adm1nP@55
```

```
line vty 0 4
transport input ssh
transport innput telnet
```

```
login local
```

Шаг 3. Установить соединение с коммутатором по протоколу SSH.

![image](https://user-images.githubusercontent.com/84719218/158619675-2122e992-54f0-4429-88aa-6d06520fac83.png)

**Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора**

Шаг 1. Посмотреть доступные параметры для клиента SSH в Cisco IOS.

