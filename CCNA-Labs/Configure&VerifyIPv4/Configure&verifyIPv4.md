Цель лабораторной работы:

Цель этого лабораторного упражнения — научиться создавать и устранять неполадки в настройке IPv4‑адресов на маршрутизаторах Cisco.

#### Задание 1:
Настройте имена хостов на маршрутизаторах R1 и R2 в соответствии с представленной топологией.

##### Топология
![[Topology.png]]

#### Задание 2:
Настройте интерфейс S0/0 на маршрутизаторе R1 (это DCE‑устройство) так, чтобы он задавал тактовую частоту 800 Кбит/с для R2. Настройте IP‑адреса на последовательных (Serial) интерфейсах маршрутизаторов R1 и R2 в соответствии с топологией. Настройте loopback‑интерфейсы, указанные на схеме, на маршрутизаторах R1 и R2.

#### Задание 3:
Используйте соответствующие команды show, чтобы проверить:
- сводную информацию обо всех настроенных IP‑адресах;
- состояние интерфейса (up/down или administratively down);
- маску подсети, применённую к интерфейсу.


#### Задание 1:
```
Router#config t 
Enter configuration commands, one per line.  End with CTRL/Z. 
Router(config)#hostname R1 

Router#config t 
Enter configuration commands, one per line. End with CTRL/Z. 
Router(config)#hostname R2 
R2(config)#
```

#### Задание 2:
```
R1(config)#int s0/1/0
R1(config-if)clock rate 800000
R1(config-if)#ip add 172.16.1.1 255.255.255.192
R1(config-if)#no shut 

R2(config)#int s0/1/0
R2(config-if)#ip add 172.16.1.2 255.255.255.192
R2(config-if)#no shut 
R2(config)#interface lo10
R2(config-if)#ip add 10.10.10.3 255.255.255.128
R2(config)#interface lo20
R2(config-if)#ip add 10.20.20.3 255.255.255.240
R2(config)#interface lo30
R2(config-if)#ip add 10.30.30.3 255.255.255.248
```

#### Задание 3:
Результаты настройки
![[R1-Checking.png]]
![[R2-Checking.png]]
Все интерфейсы настроены согласно заданию и проверка связи прошла успешно.
![[Connection-checking.png]]
