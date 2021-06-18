# GRE

### Топология

![Топология](https://github.com/ozorolol/web/blob/main/img/gre1.png)

### Сетевые настройки на маршрутизаторах и VPC:
#### R-1
  ```
  en
  conf t
  ho R-1
  int e0/0
  ip address 10.1.1.1 255.255.255.0
  no sh
  exit
  int e0/2
  ip address 20.20.1.1 255.255.255.0
  no sh
  exit
  do wr
  ```
#### R-2
  ```
  en
  conf t
  ho R-2
  int e0/0
  ip address 10.1.1.2 255.255.255.0
  no sh
  exit
  int e0/1
  ip address 10.1.2.2 255.255.255.0
  no sh
  exit
  do wr
  ```
#### R-3
  ```
  en
  conf t
  ho R-3
  int e0/1
  ip address 10.1.2.1 255.255.255.0
  no sh
  exit
  int e0/2
  ip address 20.20.2.1 255.255.255.0
  no sh
  exit
  do wr
  ```
#### VPC-8
    ```
    ip 20.20.1.2 255.255.255.0 20.20.1.1
    save
    ```
#### VPC-9
    ```
    ip 20.20.2.2 255.255.255.0 20.20.2.1
    save
    ```
### Настройки OSPF протокола динамической маршрутизации:
#### R-1
  ```
  router ospf 1
  network 10.1.1.0 0.0.0.255 area 0
  network 20.20.1.0 0.0.0.255 area 0
  exit
  do wr
  ```
#### R-2
  ```
  router ospf 1
  network 10.1.1.0 0.0.0.255 area 0
  network 10.1.2.0 0.0.0.255 area 0
  exit  
  do wr
  ```
#### R-3
  ```
  router ospf 1
  network 10.1.2.0 0.0.0.255 area 0
  network 20.20.2.0 0.0.0.255 area 0
  exit
  do wr
  ```  
### Настройка GRE тоннеля между R-1 и R-3
#### R-1
  ```
  int Tunnel1
  ip address 10.1.3.1 255.255.255.0
  ip mtu 1400
  ip tcp adjust-mss 1360
  tunnel source 10.1.1.1
  tunnel destination 10.1.2.1
  exit
  router ospf 1
  network 10.1.3.0 0.0.0.255 area 0
  exit
  do wr
  ```
#### R-3
  ```
  int Tunnel1
  ip address 10.1.3.2 255.255.255.0
  ip mtu 1400
  ip tcp adjust-mss 1360
  tunnel source 10.1.2.1
  tunnel destination 10.1.1.1
  exit
  router ospf 1
  network 10.1.3.0 0.0.0.255 area 0
  exit
  do wr
  ```
### Доказательство работы:
#### Проводим трассировку и пинг с VPC-9 до VPC-8:
![Ping/Trace](https://github.com/ozorolol/web/blob/main/img/gre2.png)
  
