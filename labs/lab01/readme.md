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

1. enable
2. show running-config
3. show interface f0/6
4. ![изображение](https://user-images.githubusercontent.com/84719218/153824640-cba0117f-87e1-4a8d-81fa-e700a1a3928a.png)
5. show flash
6. dir flash
7. conf t
8. line con 0
9. logging synhronous
10. password cisco
11. login
12. end
13. conf t
14. no ip domain-lookup
15. hostname s1
16. enable secret class
17. service password-encryption
18. banner motd # Unauthorized access is strictly prohibited. #
19. line vty 0 4
20. transport input ssh
21. intaerface vlan 1
22. ip address 192.168.1.2 255.255.255.0
23. no shutdown
24. end
25. conf t
26. line con 0
27. transport output telnet
28. end
29. show run
