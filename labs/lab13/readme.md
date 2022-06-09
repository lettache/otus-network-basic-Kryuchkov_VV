# Настройка протоколов CDP, LLDP и NTP

**Топология**

![image](https://user-images.githubusercontent.com/84719218/172783685-dc326702-77c1-4a35-8df7-8b977210318b.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 |Маска подсети    |Шлюз по умолчанию  |
| :-------------|:------------------ | :----------------------- |:--------------- |:----------------- |
| R1            | Loopback 1         | 172.16.1.1               | 255.255.255.0   |                   |
|               | G0/0/1             | 10.22.0.1                | 255.255.255.0   |                   |
| S1            | SVI VLAN 1         | 10.22.0.2                | 255.255.255.0   | 10.22.0.1         |
| S2            | SVI VLAN 1         | 10.22.0.3                | 255.255.255.0   | 10.22.0.1         |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP*

*Часть 3: Обнаружение сетевых ресурсов с помощью протокола LLDP*

*Часть 4. Настройка и проверка NTP*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Создать сеть согласно топологии.

![image](https://user-images.githubusercontent.com/84719218/172785159-d154b114-a223-4c24-a799-5e0f1235f446.png)

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
ex
```

```
conf t
int g0/0/1
ip add 10.22.0.1 255.255.255.0
no sh
ex
```

```
int loopback 1
ip add 172.16.1.1 255.255.255.0
no sh
ex
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
```

```
int range F0/2-4, F0/6-24
sh
ex
```

```
conf t
int range F0/2-24
sh
ex
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для коммутатора S2)

**Часть 2. Обнаружение сетевых ресурсов с помощью протокола CDP**

```
en
sh cdp
```

![image](https://user-images.githubusercontent.com/84719218/172790847-e9a467a8-7c05-4750-afa7-52fea69acd01.png)

```
sh cdp entry S1
```

![image](https://user-images.githubusercontent.com/84719218/172791060-bc4bf78b-0ed7-4c85-88ac-aae9670f4b8e.png)

```
en
sh cdp traffic
```

```
en
conf t
int vlan 1
ip add 10.22.0.2 255.255.255.0
no sh
ex
ip default-gateway 10.22.0.1
```

```
en
conf t
int vlan 1
ip add 10.22.0.3 255.255.255.0
no sh
ex
ip default-gateway 10.22.0.1
ex
```

```
sh cdp entry S1
```

![image](https://user-images.githubusercontent.com/84719218/172792623-a2ef4060-5102-49f2-bc15-022a20d9f581.png)

```
conf t
no cdp run
```
(Аналогично выполненна настройка для остальных устройств)

**Часть 3. Обнаружение сетевых ресурсов с помощью протокола LLDP**

```
conf t
lldp run
```
(Аналогично выполненна настройка для остальных устройств)

```
show lldp entry S2
```

```
show lldp neighbors det
```

![image](https://user-images.githubusercontent.com/84719218/172794760-6ed4b0e0-2315-46cd-96d9-7af9933e4884.png)

![image](https://user-images.githubusercontent.com/84719218/172794943-cf7b605b-9339-4789-b5d4-fbee39684cc7.png)

![image](https://user-images.githubusercontent.com/84719218/172795037-54657020-5349-43ae-9845-e395b2c31367.png)

**Часть 4. Настройка NTP**

Шаг 1. Вывести на экран текущее время.

```
show clock
```

| Дата          | Время              | Часовой пояс    |Источник времени |
| :-------------|:------------------ | :---------------|:--------------- |
| Mon Mar 1 1993| 1:8:35.170         | -               | -               |


























