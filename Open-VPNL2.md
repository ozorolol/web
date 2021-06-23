# Лабораторная работа №8 Open-VPNL2

### Топология

![Топология](https://github.com/ozorolol/web/blob/main/img/VPN_top.png)

### Состав топологии:

- Linux: Ubuntu 16.04 Server. 1024mb RAM, Ethernet 1 x3
- Linux: kali x2
- Cisco IOL: Switch - L2 образ. 256mb RAM, Ethernet portGroup - 1. x2

### Авторизация:

Для Server, Shared-1 и Shared-2

Логин: root

Пароль: eve@123

Для Linux-1 и Linux-2

Пользователь: root
Пароль: toor


### Настройка инфраструктуры:

Настроим интерфейсы на линукс машинах для получения ip. Для этого изменим файлы /etc/network/interfaces:

  ```
  nano /etc/network/interfaces
  ```

На сервере:
  
![На сервере](https://github.com/ozorolol/web/blob/main/img/VPN_int_serv.png)

На shared-1:

```
auto ens3
allow-hotplug ens3
iface ens3 inet dhcp

auto ens3.10
iface ens3 inet static
  address 10.10.10.101

```

На shared-2:

```
auto ens3
allow-hotplug ens3
iface ens3 inet dhcp

auto ens3.10
iface ens3 inet static
  address 10.10.10.102

```

А затем перезагрузим сетки на каждом командой:


```
service networking restart
```


Теперь нам нужно установить всё необходимое с помощью apt.
На сервере:

  ```
  apt update
  apt install curl
  ```
  
На клиентах:
  ```
  apt update
  apt install openvpn
  apt install easy-rsa
  apt install bridge-utils
  ```

Пока устанавливаются необходимые пакеты настроим свичи. 
На SW-1:
  ```
  en
  conf t
  ho SW-1
  vlan 10
  name Shared-Net
  exit
  int e0/1
  switchport trunk encapsulation dot1q
  switchport mode trunk
  exit
  int e0/2
  switchport mode access
  switchport access vlan 10
  exit
  do wr
  ```
На SW-2:
  ```
  en
  conf t
  ho SW-2
  vlan 10
  name Shared-Net
  exit
  int e0/1
  switchport trunk encapsulation dot1q
  switchport mode trunk
  exit
  int e0/2
  switchport mode access
  switchport access vlan 10
  exit
  do wr
  ```
Теперь нам надо настроить интерфейсы на Компьютерах. Для этого откроем терминал и впишем команду:
  ```
  nano /etc/network/interfaces
  ```
На Linux1:
![Linux1](https://github.com/ozorolol/web/blob/main/img/VPN_int_pc1.png)
На Linux2:
![Linux2](https://github.com/ozorolol/web/blob/main/img/VPN_int_pc2.png)
Теперь перезапустим сетку командой:
```
service networking restart
```

### Настройка OpenVPN

Настройку openvpn будем проводить с помощью скрипта angristan. Для этого на сервере пропишем:

  ```
  curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
  chmod +x openvpn-install.sh
  ```
 
А затем создадим клиентов (client1 и client2), два раза введя команду:

  ```
  ./openvpn-install.sh
  ```
ВАЖНО! Поменять адресса на dhcp адресс своего сервера при первой настройке Openvpn.

Теперь нам нужно подкорректировать конфигурационный файл server.conf, для это вводим команду:
  ```
  nano etc/openvpn/server.conf
  ```
Настройки:

![Настройки](https://github.com/ozorolol/web/blob/main/img/VPN_conf_serv.png)

А так же конфигурационные файлы клиентов:

На сервере вводим команду:

  ```
  nano client1.ovpn
  ```
  
Затем изменяем файл:

![client](https://github.com/ozorolol/web/blob/main/img/VPN_conf_cl.png)

И переименовываем его после командой:

  ```
  mv client1.ovpn client1.conf
  ```
  
Затем то же самое делаем для client2.

Следующим шагом будет перенос этих файлов по ssh на клиенты с сервера. Для этого заходим на клиент и пишем команды:

На Shared-1:

```
cd /etc/openvpn/
scp root@192.168.0.101:/root/client1.conf ./
```

На Shared-2:

```
cd /etc/openvpn/
scp root@192.168.0.101:/root/client2.conf ./
```

А затем снова изменим настройки интерфейсов на Shared-1 и Shared-2 заодно прокинув бриджи для работы:

![Shared1](https://github.com/ozorolol/web/blob/main/img/VPN_int_s1.png)
![Shared2](https://github.com/ozorolol/web/blob/main/img/VPN_int_s2.png)

Теперь создаем скрипт для автоматической перезагрузки сети НА КЛИЕНТАХ для работы нашего openvpn просто открыв редактор nano:

```
#!/bin/sh
/usr/bin/systemctl restart networking
```

После чего сохраняем его как /etc/openvpn/up.sh и делаем исполняемым: 

```
chmod +x up.sh
```

Разрешаем openvpn стратровать после перезагрузки:

На сервере:

```
systemctl enable openvpn@server
```


На клиенте:
```
systemctl enable openvpn@client1
nano /etc/default/openvpn
```

Удаляем # перед строкой AUTOSTART = "all" и сохраняем файл.

Теперь достаточно перезапустить серер и клиенты и проверить работу:

```
reboot
```

### Доказательства работы:

1. Shared-1 пингует Shared-2:

![ping cl to cl](https://github.com/ozorolol/web/blob/main/img/VPN_ping_s1-s2.png)

2. Linux-1 пингует сервер:

![ping pc to serv](https://github.com/ozorolol/web/blob/main/img/VPN_ping_pc-serv.png)

3. Linux-1 пингует Linux-2:

![ping pc to serv](https://github.com/ozorolol/web/blob/main/img/VPN_ping_pc-pc.png)

4. Трасировка между Linux-1 и Linux-2 идет в 1 хоп:

![trace](https://github.com/ozorolol/web/blob/main/img/VPN_trace_pc-pc.png)
