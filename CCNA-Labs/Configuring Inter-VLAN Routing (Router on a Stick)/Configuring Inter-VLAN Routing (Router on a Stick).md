**Цель лабораторной работы:**
Цель этого лабораторного упражнения — настроить маршрутизатор для обеспечения межвлановой маршрутизации (inter‑VLAN routing). По умолчанию узлы в одной VLAN не могут взаимодействовать с узлами в другой VLAN без маршрутизатора, который осуществляет маршрутизацию между этими VLAN.

**Назначение лабораторной работы:**
В большинстве сетей обычно используется более одной VLAN, и при необходимости узлы в этих VLAN должны иметь возможность обмениваться данными друг с другом. В данном примере у вас нет коммутатора 3‑го уровня (Layer 3 switch), поэтому для маршрутизации необходимо использовать маршрутизатор.

#### Топология
![[Configuring Inter-VLAN Routing (Router on a Stick)/images/topology.png]]
---

**Задание 1:**
В рамках подготовки к настройке VLAN задайте имена хостов для коммутаторов SW1 и SW2 в соответствии с приведённой топологией.

**Задание 2:**
Настройте и проверьте, что коммутаторы SW1 и SW2 работают в режиме VTP Transparent. Оба коммутатора должны находиться в домене VTP с именем CISCO. Защитите сообщения VTP с помощью пароля ciscopa55.

**Задание 3:**
Настройте и проверьте, что интерфейс FastEthernet0/1 между коммутаторами SW1 и SW2 работает как транк 802.1Q, и настройте VLAN в соответствии с приведённой выше топологией. Назначьте порты указанным VLAN и настройте интерфейс FastEthernet0/2 на SW1 как транковый. Для VLAN 20 кадры Ethernet должны передаваться без тегов. Помните, что в транках 802.1Q без тегов передаются кадры только для native‑VLAN.

**Задание 4:**
Настройте IP‑адреса на маршрутизаторах R2, R3 и R4 в соответствии с топологией.

**Задание 5:**
Настройте подинтерфейсы на интерфейсе FastEthernet0/0 маршрутизатора R1 в соответствии с VLAN, указанными в топологии. Также настройте интерфейс VLAN10 на коммутаторе Sw2 с IP‑адресом 10.0.10.2/28.

**Задание 6:**
Проверьте связность сети, выполнив ping с маршрутизатора R1 до маршрутизаторов R2, R3 и R4.

#### Задание 1:
Настройка именов устройств, выполнялась в соответствии с заданием, пример с настройкой именов можно просмотреть в одной из первых лабораторных работах.
#### Задание 2:
Настройка VTP домена и установка пароля проводилась в соответствии с заданием пример настройки можно посмотреть в одной из первых лабораторных. 
#### Задание 3:
Зададим VLANы в соответствии с заданием.
```
Enter configuration commands, one per line. End with CTRL/Z.
SW1(config)#interface fastethernet0/1
SW1(config-if)#switchport mode trunk
SW1(config-if)#exit
SW1(config)#vlan 10
SW1(config-vlan)#name SALES
SW1(config-vlan)#exit
SW1(config)#vlan 20
SW1(config-vlan)#name TECH
SW1(config-vlan)#exit
SW1(config)#vlan 30
SW1(config-vlan)#name ADMIN
SW1(config-vlan)#exit
SW1(config)#vlan 40
SW1(config-vlan)#name TEST
SW1(config-vlan)#exit
SW1(config)#interface fastethernet0/2
SW1(config-if)#switchport mode trunk
SW1(config-if)#switchport trunk native vlan 20
SW1(config-if)#exit
SW1(config)#interface fastethernet0/3
SW1(config-if)#switchport mode access
SW1(config-if)#switchport access vlan 20
SW1(config-if)#end
SW1#show interfaces trunk
```
![[vlan-checking-sw1.png]]
#### Задание 4:
Зададим  IP адресацию на интерфейсах маршрутизаторов R2, R3 и R4.
```
R2#show ip int br
Interface IP-Address OK? Method Status Protocol
FastEthernet0/0 unassigned YES unset administratively down down
FastEthernet0/1 unassigned YES unset administratively down down
Vlan1 unassigned YES unset administratively down down
R2#
R2#conf t
Enter configuration commands, one per line. End with CNTL/Z.
R2(config)#int fa0/0
R2(config-if)#ip add 10.0.20.2 255.255.255.128
R2(config-if)#no shutdown
R2(config-if)#exit
R2(config)#exit
R2#
```
На интерфейсах маршрутизаторов R3 и R4 адресация задается аналогичным методом.
#### Задание 5:
Настроим субинтерфейсы для межвлановой маршрутизации.
```
R1#config t
Enter configuration commands, one per line. End with CTRL/Z.
R1(config)#interface fastethernet0/0
R1(config-if)#description “Connected To Switch Trunk Fa0/2”
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface fastethernet0/0.10
R1(config-subif)#description Subinterface For VLAN10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip address 10.0.10.1 255.255.255.240
R1(config-subif)#exit
R1(config)#interface fastethernet0/0.20
R1(config-subif)#description Subinterface For VLAN20
R1(config-subif)#encapsulation dot1Q 20 native
R1(config-subif)#ip address 10.0.20.1 255.255.255.128
R1(config-subif)#exit
R1(config)#interface fastethernet0/0.30
R1(config-subif)#description Subinterface For VLAN30
R1(config-subif)#ip address 10.0.30.1 255.255.255.248
R1(config-subif)#exit
R1(config)#interface fastethernet0/0.40
R1(config-subif)#description Subinterface For VLAN40
R1(config-subif)#encapsulation dot1Q 40
R1(config-subif)#ip address 10.0.40.1 255.255.255.224
R1(config-subif)#end
R1#show ip interface brief
```
![[r1-show-int.png]]
```
Sw2(config)#interface vlan1
Sw2(config-if)#shutdown
Sw2(config)#interface vlan10
Sw2(config-if)#ip address 10.0.10.2 255.255.255.240
Sw2(config-if)#no shutdown
Sw2(config)#^Z
Sw2#show ip interface brief
```
![[sw2-show-int.png]]
#### Задание 6:
Проведем проверку доступности устройств из разных VLANов.
![[connection-check.png]]
От R1 пакеты проходят во все сети, так как и должно быть. Проверим прохождение пакетов от R2 к R4. **Пакеты не должны проходить**. Т.к. пакеты не могут общатся напрямую.
![[r2-to-r4-connection-fail.png]]