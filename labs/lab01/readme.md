# Базовая настройка коммутатора

**Задачи:**

  *1. Проверка конфигурации коммутатора по умолчанию*
  
  *2. Создание сети и настройка основных параметров устройства*
  
   - Настроить базовые параметры коммутатора
   - Настроить IP-адрес для ПК.

  *3. Проверка сетевых подключений*
  
   - Отобразить конфигурацию устройства
   - Протестировать сквозное соединение, отправив эхо-запрос
   - Протестировать возможности удаленного управления с помощью Telnet



**Топология**

![Base_shema](https://user-images.githubusercontent.com/84719218/153394318-2546512e-c22a-43e1-a538-03ca255b2875.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес / префикс|
| ------------- |:------------------:| -----:|
| S1            | VLAN1              | 192.168.1.2 /24 |
| PC-A          | NIC                | 192.168.1.10 /24   |

**Решение:**

**1. Создание сети и проверка настроек коммутатора по умолчанию**

```
enable
show running-config
show interface f0/6
show flash
```

**2. Настройка базовых параметров сетевых устройств**

```
conf t
no ip domain-lookup
hostname S1
service password-encryption
enable secret class
banner motd # Unauthorized access is strictly prohibited. #
```

  а) Настройка порта консоли

```
line console 0
logging synhronous
password cisco
login
end
```

  б) Настройка виртуальных сессий

```
conf t
line vty 0 4
enable secret class
line vty 0 4
transport input ssh
end
```

  в) Назначание IP-адреса интерфейса SVI
  
```
conf t
interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
end
conf t
ip default-getaway 192.168.1.1
```

   г) Изменение доступа к административным интерфейсам на vlan 99

```
conf t
interface FastEthernet 0/24
switchport access vlan 99
```
(аналогично выполненна для других интерфейсов GigabitEthernet0/1 и GigabitEthernet0/2


   д) Настройка IP-адреса на компьютере PC-A.
   
```
1)	Перейти в Панель управления. (Control Panel)
2)	В представлении «Категория» выбрать « Просмотр состояния сети и задач».
3)	Щелкнуть Изменение параметров адаптера на левой панели.
4)	Щелкнуть правой кнопкой мыши интерфейс Ethernet и выберать «Свойства» .
5)	Выберать Протокол Интернета версии 4 (TCP/IPv4) > Свойства.
6)	Выберать Использовать следующий IP-адрес и ввести IP-адрес 192.168.1.10 и маску подсети 255.255.255.0  и нажать ОК.

```

**3. Проверка сетевых подключений**

```
show run
```
![изображение](https://user-images.githubusercontent.com/84719218/154054938-1d8d9e2b-8111-470b-824f-f7b91d100643.png)

![изображение](https://user-images.githubusercontent.com/84719218/154054961-19d73a26-5529-46fc-b912-e8a55d0d01fd.png)

![изображение](https://user-images.githubusercontent.com/84719218/154054975-5670163a-7dcf-431c-9fbd-76f5e812de1e.png)

Проверка параметров vlan 1

```
show interface vlan 1
```
![изображение](https://user-images.githubusercontent.com/84719218/154058682-9d6f4c35-bc66-4833-bec2-ebed48a3113e.png)

   а) Тестирование сквозного соединения, отправив эхо-запрос.

```
C:\> ping 192.168.1.10 
```

```
C:\> ping 192.168.1.2
```

![изображение](https://user-images.githubusercontent.com/84719218/154058881-85b1d14a-8592-4c4d-960d-53a3cf7f2176.png)

![изображение](https://user-images.githubusercontent.com/84719218/154058949-8d693475-aa73-45ba-a087-5ca56f2756e8.png)

   б) Проверка удаленного управления коммутатором S1.

![изображение](https://user-images.githubusercontent.com/84719218/154066213-9298f7e2-7caa-4502-85a1-d8a0902c00b7.png)






