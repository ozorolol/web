# Лабораторная работа №1 iptables

### Топология

![Топология](https://github.com/ozorolol/web/blob/main/img/ipt1.png)

### Состав топологии

- Linux: Ubuntu 16.04 Server. 1024mb RAM, Ethernet 1 x2
- Cisco IOL: Switch - L2 образ. 256mb RAM x1
- Linux: kali x1

### Настройки сети

- На Firewall:
  ```
  auto ens4 
  allow-hotplug ens4
  iface ens4 inet dhcp
  
  auto ens3
  allow-hotplug ens3
  iface ens3 inet static
      address 10.10.100.254/24
  ```
- На Server:
  ```
  auto ens3
  allow-hotplug ens3
  iface ens3 inet dhcp
  ```
- На PC-1:
  ![Сетка](https://github.com/ozorolol/web/blob/main/img/ipt5.png)

  
### Настройки политик iptables

  ```
  iptables -A INPUT -p tcp --dport 22 -d 192.168.71.130 -j ACCEPT
  iptables -A FORWARD -p tcp --dport 80 -d 192.168.71.131 -j ACCEPT
  iptables -A FORWARD -d 192.168.71.131 -j DROP
  ```
  
### Доказательство работы. 
#### С PC-1 пытаемся пропинговать и подключиться по SSH на наш сервер:
![Пинги](https://github.com/ozorolol/web/blob/main/img/ipt3.png)
#### Как видим, у нас это не получается. 
#### Теперь попытаемся достучаться по 80 порту:
![Веб](https://github.com/ozorolol/web/blob/main/img/ipt4.png)
