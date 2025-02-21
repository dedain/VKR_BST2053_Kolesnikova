**Первичная упрощенная схема сети: <br>**
![1первичная](https://github.com/user-attachments/assets/85f76cfb-658e-4eed-887e-7c4a8256036a)


**Схема подключения и взаимодействия устройств в разрабатываемой корпоративной сети: <br>**
![2полная](https://github.com/user-attachments/assets/1bd0dbb7-d000-49e7-9dd4-213e00de7230)


**Реализация StackWise Virtual: <br>**
![3стек](https://github.com/user-attachments/assets/d537afff-50f4-4ca6-93b5-2a18e83a93bf)


**Логическая птопология сети: <br>**
![4логическая топология](https://github.com/user-attachments/assets/fd966cda-0106-40d8-ae9e-9b43e6ea6bea)


**Зоны OSPF: <br>**
![5оспф](https://github.com/user-attachments/assets/3421e610-d4eb-476f-9bd3-8947c0633a31)

## Конфигурация стекирования Центральный Офис
**На CORE1**
~~~
en
conf t
stackwise-virtual
domian 100
priority 15
int range gi0/3-4
stackwise-virtual link1
ex
int gi0/14
stackwise-virtual dual-active-detection
ex
do wr
ex
reload
~~~
**На RSRVCORE1**
~~~
en
conf t
stackwise-virtual
domian 100
priority 10
int range gi0/3-4
stackwise-virtual link1
ex
int gi0/14
stackwise-virtual dual-active-detection
ex
do wr
ex
reload
~~~


## Конфигурация стекирования Офис Продаж
**На CORE2**
~~~
en
conf t
stackwise-virtual
domian 200
priority 15
int range gi0/3-4
stackwise-virtual link1
ex
int gi0/12
stackwise-virtual dual-active-detection
ex
do wr
ex
reload
~~~
**На RSRVCORE2**
~~~
en
conf t
stackwise-virtual
domian 200
priority 10
int range gi0/3-4
stackwise-virtual link1
ex
int gi0/12
stackwise-virtual dual-active-detection
ex
do wr
ex
reload
~~~
### После выполнения перечисленных команд l3 коммутаторы объеденины в стеки, дальнейшая настройка может проводиться на любом из коммутатров, распространяться будет на весь стек

## Конфигурация стекирования Центральный Офис
~~~
int Port-channel1
  no switchport
  ip address 203.0.113.4 255.255.255.252
  no shutdown
  ex
int range gi0/1-2
  channel-group 1 mode active
  ex
~~~
~~~
int Port-channel2
  no switchport
  ip address 192.168.1.103 255.255.255.224
  no shutdown
  ex
int range gi0/5-6, gi0/11
  channel-group 2 mode active
  ex
~~~
~~~
int Port-channel3
  switchport mode trunk
  spanning-tree portfast trunk 
  ex
int range gi0/7-8, gi0/12
  switchport
  switchport mode trunk
  channel-group 3 mode active
  ex
~~~
~~~
int Port-channel4
  no switchport
  ip address 192.168.1.105 255.255.255.224
  no shutdown
  ex
int range gi0/9-10, gi0/13
  channel-group 4 mode active
  ex
~~~
на dist1
~~~
int Port-channel2
  no switchport
  ip address 192.168.1.98 255.255.255.224
  no shutdown
  ex
int range gi0/9-10, gi0/13
  channel-group 4 mode active
  ex
~~~
на sw481
~~~
int Port-channel3
  switchport mode trunk
  spanning-tree portfast trunk 
  ex
int range gi0/7-8, gi0/12
  switchport
  switchport mode trunk
  channel-group 3 mode active
  ex
~~~

на dist2
~~~
int Port-channel4
  no switchport
  ip address 192.168.1.99 255.255.255.224
  no shutdown
  ex
int range gi0/9-10, gi0/13
  channel-group 4 mode active
  ex
~~~

## Конфигурация стекирования Офис продаж
~~~
int Port-channel1
  no switchport
  ip address 203.0.113.6 255.255.255.252
  no shutdown
  ex
int range gi0/1-2
  channel-group 1 mode active
  ex
~~~
~~~
int Port-channel2
  no switchport
  ip address 192.168.1.106 255.255.255.224
  no shutdown
  ex
int range gi0/5-6, gi0/11
  channel-group 2 mode active
  ex
~~~
~~~
int Port-channel3
  no switchport
  ip address 192.168.1.107 255.255.255.224
  no shutdown
  ex
int range gi0/7-8, gi0/12
  channel-group 3 mode active
  ex
~~~
на dist4
~~~
int Port-channel2
  no switchport
  ip address 192.168.1.100 255.255.255.224
  no shutdown
  ex
int range gi0/5-6, gi0/11
  channel-group 2 mode active
  ex
~~~
на dist5
~~~
int Port-channel3
  no switchport
  ip address 192.168.1.104 255.255.255.224
  no shutdown
  ex
int range gi0/7-8, gi0/12
  channel-group 3 mode active
  ex
~~~

## Конфигурация OSPF
Конфигурация OSPF для Core1+RSRVCore1
~~~
conf t
router ospf 10
router-id 1.1.1.1
network 192.168.1.96 0.0.0.31 area 0
network 192.168.2.0 0.0.0.7 area 0
network 192.168.1.32 0.0.0.15 area 0
network 192.168.1.48 0.0.0.63 area 1 
network 192.168.1.0 0.0.0.31 area 1
~~~
Конфигурация OSPF для Core2+RSRVCore2
~~~
conf t
router ospf 10
router-id 2.2.2.2
network 192.168.2.0 0.0.0.7 area 0
network 192.168.1.96 0.0.0.31 area 0
network 192.168.0.128 0.0.0.127 area 2
network 192.168.0.0 0.0.0.127 area 3
~~~

Конфигурация OSPF для DIST1
~~~
conf t
router ospf 10
 router-id 3.3.3.3
 network 192.168.1.48 0.0.0.63 area 1
 network 192.168.1.96 0.0.0.31 area 0
exit
~~~

Конфигурация OSPF для DIST2
~~~
conf t
router ospf 10
 router-id 4.4.4.4
 network 192.168.1.0 0.0.0.31 area 1
 network 192.168.1.96 0.0.0.31 area 0
exit
~~~
Конфигурация OSPF для DIST4
~~~
conf t
router ospf 10
 router-id 5.5.5.5
 network 192.168.0.128 0.0.0.127 area 2
 network 192.168.1.96 0.0.0.31 area 0
exit
~~~

Конфигурация OSPF для DIST5
~~~
conf t
router ospf 10
 router-id 6.6.6.6
 network 192.168.0.0 0.0.0.127 area 3
 network 192.168.1.96 0.0.0.31 area 0
exit
~~~
