# Лабораторная работа №10 MPLS

### Топология:

![Топология](https://github.com/ozorolol/web/blob/main/img/mpls1.png)

### Сетевые настройки на маршрутизаторах и VPC
#### MPLS-1
  ```
  en
  conf t
  ho MPLS-1
  int loopback0 
  ip address 10.10.10.10 255.255.255.255
  no sh
  exit
  int e0/2
  ip address 192.168.1.1 255.255.255.0
  no sh
  exit
  int e0/0
  ip address 1.1.1.1 255.255.255.252 
  no sh 
  exit 
  int e0/1 
  ip address 2.2.2.1 255.255.255.252 
  no sh 
  exit 
  do wr 
  ```
#### MPLS-2
  ```
  en
  conf t
  ho MPLS-2
  int loopback0 
  ip address 20.20.20.20 255.255.255.255
  no sh
  exit
  int e0/0
  ip address 1.1.1.2 255.255.255.252 
  no sh 
  exit 
  int e0/1 
  ip address 3.3.3.2 255.255.255.252 
  no sh 
  exit 
  do wr 
  ```
#### MPLS-3
  ```
  en
  conf t
  ho MPLS-3
  int loopback0 
  ip address 30.30.30.30 255.255.255.255
  no sh
  exit
  int e0/0
  ip address 4.4.4.2 255.255.255.252 
  no sh 
  exit 
  int e0/1 
  ip address 2.2.2.2 255.255.255.252 
  no sh 
  exit 
  do wr 
  ```
#### MPLS-4
  ```
  en
  conf t
  ho MPLS-4
  int loopback0 
  ip address 40.40.40.40 255.255.255.255
  no sh
  exit
  int e0/2
  ip address 192.168.2.1 255.255.255.0
  no sh
  exit
  int e0/0
  ip address 3.3.3.1 255.255.255.252 
  no sh 
  exit 
  int e0/1 
  ip address 4.4.4.1 255.255.255.252 
  no sh 
  exit 
  do wr 
  ```
#### MPLS-PC-1
  ```
  ip 192.168.1.10 255.255.255.0 192.168.1.1
  save
  ```
#### MPLS-PC-2
  ```
  ip 192.168.2.10 255.255.255.0 192.168.2.1
  save
  ```
###  Настройки протокола динамической маршрутизации OSPF
#### MPLS-1
```
router ospf 1  
passive-interface default  
no passive-interface e0/0 
no passive-interface e0/1 
network 192.168.1.0 0.0.0.255 area 1  
network 1.1.1.0 0.0.0.3 area 0 
network 2.2.2.0 0.0.0.3 area 0 
network 10.10.10.10 0.0.0.0 area 0 
exit 
do wr
```
#### MPLS-2
```
router ospf 1  
network 1.1.1.0 0.0.0.3 area 0 
network 3.3.3.0 0.0.0.3 area 0 
network 20.20.20.20 0.0.0.0 area 0 
exit 
do wr
```
#### MPLS-3
```
router ospf 1  
network 2.2.2.0 0.0.0.3 area 0 
network 4.4.4.0 0.0.0.3 area 0 
network 30.30.30.30 0.0.0.0 area 0 
exit
do wr
```
#### MPLS-4
```
router ospf 1  
passive-interface default 
no passive-interface e0/0 
no passive-interface e0/1 
network 192.168.2.0 0.0.0.255 area 2 
network 3.3.3.0 0.0.0.3 area 0 
network 4.4.4.0 0.0.0.3 area 0 
network 40.40.40.40 0.0.0.0 area 0 
exit 
do wr
```

### Настройки MPLS на маршрутизаторах
#### MPLS-1
  ```
  ip cef  
  mpls ip  
  mpls label protocol ldp  
  mpls ldp router-id loopback 0  
  int e0/0 
  mpls ip 
  mpls mtu 1410 
  exit 
  nt e0/1 
  mpls ip 
  mpls mtu 1410 
  exit
  do wr
  ```
#### MPLS-2
  ```
  ip cef 
  mpls ip 
  mpls label protocol ldp 
  mpls ldp router-id loopback 0 
  int e0/0 
  mpls ip 
  mpls mtu 1410 
  exit 
  int e0/1 
  mpls ip 
  mpls mtu 1410 
  exit
  do wr
  ```
#### MPLS-3 
  ```
  ip cef 
  mpls ip 
  mpls label protocol ldp 
  mpls ldp router-id loopback 0 
  int e0/0 
  mpls ip 
  mpls mtu 1410 
  exit 
  int e0/1 
  mpls ip 
  mpls mtu 1410 
  exit
  do wr
  ```
#### MPLS-4
  ```
  ip cef 
  mpls ip 
  mpls label protocol ldp 
  mpls ldp router-id loopback 0 
  int e0/0 
  mpls ip 
  mpls mtu 1410 
  exit 
  int e0/1 
  mpls ip 
  mpls mtu 1410 
  exit
  do wr
  ```
### Доказательство работы:
![Доказательство№1](https://github.com/ozorolol/web/blob/main/img/mpls2.png)
![Доказательство№2](https://github.com/ozorolol/web/blob/main/img/mpls3.png)
