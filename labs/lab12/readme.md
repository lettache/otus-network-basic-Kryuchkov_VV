# Настройка NAT для IPv4

**Топология**

![image](https://user-images.githubusercontent.com/84719218/172834664-09c854df-8c77-4616-af5f-be56c1915278.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес                 | Маска подсети   |
| :-------------|:------------------ | :----------------------- |:--------------- |
| R1            | G0/0/0             | 209.165.200.230          | 255.255.255.248 |
|               | G0/0/1             | 192.168.1.1              | 255.255.255.0   |
| R2            | G0/0/0             | 209.165.200.225          | 255.255.255.248 |
|               | Lo1                | 209.165.200.1            | 255.255.255.224 |
| S1            | VLAN 1             | 192.168.1.11             | 255.255.255.0   |
| S2            | VLAN 1             | 192.168.1.12             | 255.255.255.0   |
| PC-A          | NIC                | 192.168.1.2              | 255.255.255.0   |
| PC-B          | NIC                | 192.168.1.3              | 255.255.255.0   |


**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Настройка и проверка NAT для IPv4*

*Часть 3. Настройка и проверка PAT для IPv4*

*Часть 4. Настройка и проверка статического NAT для IPv4*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Подключить кабеля сети согласно приведенной топологии

![image](https://user-images.githubusercontent.com/84719218/172836445-91ec3676-676e-405d-bc43-5040a2e0791c.png)

Шаг 2. Произведите базовую настройку маршрутизаторов

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
```

```
int g0/0/0
ip add 209.165.200.230 255.255.255.248
no sh
ex
int g0/0/1
ip add 192.168.1.1 255.255.255.0
no sh
ex
```

```
ip route 0.0.0.0 0.0.0.0 g0/0/0
```

```
copy running-config startup-config
```
(Аналогично выполненна настройка для маршрутизатора R2)

Шаг 3. Настройте базовые параметры каждого коммутатора.

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
int range F0/2-17, F0/19-24
sh
ex
```

```
int vlan 1
ip add 192.168.1.12 255.255.255.0
no sh
ex
```

```
conf t
int range F0/2-4, f0/7-24
sh
ex
```

```
int vlan 1
ip add 192.168.1.11 255.255.255.0
no sh
ex
```

```
copy running-config startup-config
```
(Аналогично выполненна настройка для коммутатора S2)

**Часть 2. Настройка и проверка NAT для IPv4**

Шаг 1. Настроить NAT на R1, используя пул из трех адресов 209.165.200.226-209.165.200.228. 

```
conf t
access-list 1 permit 192.168.1.0 0.0.0.255
```

```
ip nat pool PUBLIC_ACCESS 209.165.200.226 209.165.200.228 netmask 255.255.255.248 
```

```
ip nat inside source list 1 pool PUBLIC_ACCESS
```

```
int g0/0/1
ip nat inside
ex
```

```
int g0/0/0
ip nat outside
end
```

Шаг 2. Проверить конфигурацию. 

```
ping 209.165.200.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174970978-0b44265d-0360-42c2-8de6-6b73edfc3549.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174970794-f6459e61-be5e-48e0-960a-c7993453757f.png)

```
ping 209.165.200.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174971294-01148cdf-188a-42e2-9a53-e15cd4c66e36.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174971407-713a2b12-489b-42fb-9613-77debaf136c6.png)

```
ping -t 209.165.200.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174971781-192308ac-85bf-4569-857f-4c4f96f7c405.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174972023-8105ca2a-201a-4b97-badd-e8ac1b1c52ed.png)

```
clear ip nat translations * 
clear ip nat statistics  
```

Шаг 4. На R1 удалить команды преобразования nat pool.

```
conf t
no ip nat inside source list 1 pool PUBLIC_ACCESS overload 
no ip nat pool PUBLIC_ACCESS
```

Шаг 5. Добавить команду PAT overload, указав внешний интерфейс.

```
ip nat inside source list 1 interface g0/0/0 overload 
```

Шаг 6. Протестировать и проверить конфигурацию. 

```
ping 209.165.200.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174975303-ac98ea4b-7f5f-4d50-99f8-8ffea8c740c8.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174976086-101b207b-a519-49e4-979d-bc36872c331d.png)

```
ping -t 209.165.200.1
```

![изображение](https://user-images.githubusercontent.com/84719218/174976413-fca01ffe-bcf5-4941-a450-922ed78194a3.png)

![изображение](https://user-images.githubusercontent.com/84719218/174977948-3c28f7c8-4778-42ac-abef-86208ec948b4.png)

![изображение](https://user-images.githubusercontent.com/84719218/174978040-dd346965-a0d1-499f-9ce2-f8c4f64837c9.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174978193-6ec0b5f5-68c1-4fe0-99b2-6e9eb5b14940.png)

**Часть 4. Настройка и проверка статического NAT для IPv4.**

Шаг 1. На R1 очистить текущие трансляции и статистику.

```
clear ip nat translation * 
clear ip nat statistics  
```

Шаг 2. На R1 настроить команду NAT, необходимую для статического сопоставления внутреннего адреса с внешним адресом.

```
conf t
ip nat inside source static 192.168.1.2 209.165.200.229 
ex
```

Шаг 3. Протестировать и проверить конфигурацию.

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174979368-65d12bec-2dc0-456a-bc2a-7b8e039adf4f.png)

```
ping 209.165.200.229
```

![изображение](https://user-images.githubusercontent.com/84719218/174979811-4249c932-0311-4181-acd3-6b7a57314595.png)

```
show ip nat translations
```

![изображение](https://user-images.githubusercontent.com/84719218/174979993-99de35e5-ec8f-4faf-9908-b30a48ad4d14.png)
