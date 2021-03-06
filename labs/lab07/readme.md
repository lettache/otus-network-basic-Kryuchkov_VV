# Развертывание коммутируемой сети с резервными каналами

**Топология**

![изображение](https://user-images.githubusercontent.com/84719218/161692841-812bab7a-1d0a-4d44-acda-b7fe9746065b.png)

**Таблица адресации**

| Устройство    | Интерфейс          | IP-адрес        |Маска подсети      |
| :-------------|:------------------ | :-------------- |:------------------|
| S1            | VLAN 1             | 192.168.1.1     | 255.255.255.0     |
| S2            | VLAN 1             | 192.168.1.2     | 255.255.255.0     |
| S3            | VLAN 1             | 192.168.1.3     | 255.255.255.0     |

**Задачи**

*Часть 1. Создание сети и настройка основных параметров устройства*

*Часть 2. Выбор корневого моста*

*Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов*

*Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов*

**Решение:**

**Часть 1. Создание сети и настройка основных параметров устройства**

Шаг 1. Создать сеть согласно топологии.

![изображение](https://user-images.githubusercontent.com/84719218/161693608-51c7ccb5-36b0-4c60-8727-b24f1ef6fc15.png)

Шаг 2:	Выполнить инициализацию и перезагрузку коммутаторов.

```
en
reload
```
(Аналогично выполненна настройка для коммутаторов S2 и S3)

Шаг 3:	Настройте базовые параметры каждого коммутатора.

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
interface vlan 1
ip address 192.168.1.1 255.255.255.0
no shutdown
end
```

```
copy running-config startup-config
```

(Аналогично выполненна настройка для коммутаторов S2 и S3)

Шаг 4:	Проверить связь.

![изображение](https://user-images.githubusercontent.com/84719218/161697869-d172ae82-6059-428d-a71a-375e8415748c.png)

![изображение](https://user-images.githubusercontent.com/84719218/161697922-492601c1-2371-4a98-8987-3862d48113bc.png)

![изображение](https://user-images.githubusercontent.com/84719218/161698034-00c69bce-99ef-4a21-9f66-66859d4b1908.png)

**Часть 2:	Определение корневого моста**

Шаг 1:	Отключить все порты на коммутаторах.

```
en
conf t
int range f0/1-24
shutdown
```
(Аналогично выполненна настройка для коммутаторов S2 и S3)

Шаг 2:	Настроить подключенные порты в качестве транковых.

```
conf t
int range f0/1-4
switchport mode trunk
end
```
(Аналогично выполненна настройка для коммутаторов S2 и S3)

Шаг 3:	Включить порты F0/2 и F0/4 на всех коммутаторах.

```
conf t
int f0/2
no shutdown
ex
int f0/4
no shutdown
ex
```
(Аналогично выполненна настройка для коммутаторов S2 и S3)

Шаг 4:	Отобразить данные протокола spanning-tree.

```
show spanning-tree
```

(Аналогичная команда для коммутаторов S2 и S3)

![изображение](https://user-images.githubusercontent.com/84719218/162141542-dcbb727c-a00c-4186-876c-6af75b1c68dd.png)

![изображение](https://user-images.githubusercontent.com/84719218/162141803-e458d3c2-92df-4459-a707-930094e8dc7b.png)

![изображение](https://user-images.githubusercontent.com/84719218/162141927-a44e608a-ae28-4222-a2f5-bd22919e42d3.png)

Какой коммутатор является корневым мостом? - S2

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста? - коммутатор S2 с самым низким значением MAC-адреса

Какие порты на коммутаторе являются корневыми портами? - Порт f0/4

Какие порты на коммутаторе являются назначенными портами? - Порт f0/2

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? - Порт f0/2

Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта? - для предотвращения петли

**Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов**

Шаг 1:	Определить коммутатор с заблокированным портом.

```
show spanning-tree
```
(Аналогичная команда для коммутатора S3)

![изображение](https://user-images.githubusercontent.com/84719218/162148246-e437461d-da26-4920-86cb-45c35a0f7fa8.png)

![изображение](https://user-images.githubusercontent.com/84719218/162148365-f8a9b746-8c66-48a4-89bb-01b6ec56c993.png)

Шаг 2:	Изменить стоимость порта.

```
conf t
int f0/2
spanning-tree cost 18
end
```

Шаг 3:	Просмотреть изменения протокола spanning-tree.

```
show spanning-tree
```

Шаг 4:	Удалить изменения стоимости порта.

```
conf t
int f0/2
no spanning-tree cost 18
end
sh sp
```

**Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов**

a)

```
conf t
int f0/1
no sh
ex
int f0/3
no sh
ex
```
(Аналогично выполненна настройка для коммутаторов S2 и S3)

b)

```
sh sp
```

![изображение](https://user-images.githubusercontent.com/84719218/162162701-1ad47245-0474-4ab1-ad4e-d79703ddbf2b.png)

![изображение](https://user-images.githubusercontent.com/84719218/162162912-e4b5f74a-9bc7-46ef-a444-358dfad1a158.png)

Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? - f0/3

Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах? - порт с меньшим номером

