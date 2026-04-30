# OSPF Layer2 WAN P2MP

## توضیح LAB

### در این LAB ما از یک WAN لایه 2 استفاده کرده ایم در ISP سویچ هایی مثلاً WPLS یا مترواترنت قرار گرفته و این ابر مخابرات WAN لایه 2 استHub and Spoke  بوده و میخواهیم OSPF را ه اندازی کنیم.
### اگر در این ابر مخابرات روترها فقط دفتر مرکزی را می بینند ما در اینجا نیاز به DR و BDR نداریم پس میرویم سراغ نتورک تایپ هایی که DR و BDR ندارند.
---


## طراحی شبکه

### فعلا بحث Multi Area نیست و مینیمم کانفیگ OSPF در همه روترها کانفیگ میکنیم

```cisco
RTR-HQ(config)#router ospf 1
RTR-HQ(config-router)#network 0.0.0.0 255.255.255.255 area 0
RTR-HQ(config-router)#passive-interface ethernet 0/0
RTR-HQ(config-router)#exit

```
### این دستورات در همه روترها زده خواهد شد.

## تحلیل عملکرد روترها

### روترها وقتی در مرحله 2-Way اند یعنی دارن DR انتخاب مبکنند مدت زمان انتخاب DR به مدت Wait time است که ایم مقدار به صورت اتوماتیک به اندازه Dead Time که 40 ثانیه است میباشد.یهنی روترها به اندازه 40 ثانیه تو این مرحله2-way گیر میکنند تا بررسی کنند کی قراره DR شود.

```cisco
RTR-HQ#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     1   2WAY/DROTHER    00:00:38    192.168.10.11   Ethernet1/0
192.168.10.12     1   FULL/BDR        00:00:36    192.168.10.12   Ethernet1/0
192.168.10.13     1   FULL/DR         00:00:39    192.168.10.13   Ethernet1/0

```
### وضعیت اینترفیس هر روتر را بررسی میکنیم. که در چه نقشی قرار گرفته اند.

```cisco

RTR-HQ#sh ip ospf interface ethernet 1/0
Ethernet1/0 is up, line protocol is up
  Internet Address 192.168.10.10/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.10, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State BDR, Priority 1


RTR-B1#sh ip ospf interface ethernet 1/1
Ethernet1/1 is up, line protocol is up
  Internet Address 192.168.10.11/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.11, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State DR, Priority 1


RTR-B2#sh ip ospf interface ethernet 1/2
Ethernet1/2 is up, line protocol is up
  Internet Address 192.168.10.12/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.12, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State DR, Priority 1

RTR-B3#sh ip ospf interface ethernet 1/3
Ethernet1/3 is up, line protocol is up
  Internet Address 192.168.10.13/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.13, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State DR, Priority 1


```


## OSPF Area Design

| روتر    | روتر آیدی | وضعیت |
|--------|--------|---------|
| RTR-HQ | 192.168.10.10| BDR |
| RTR-B1 | 192.168.10.11| DR |
| RTR-B2 | 192.168.10.12| DR |
| RTR-B3 | 192.168.10.13| DR |

**الان روت های همدیگر را یاد نگرفته اند چون دفتر مرکزی باید DR باشد چون روا همه را بلد است و شعبه ها همشون DRother شن** 

### پرایوتی شعبه ها را صفر میکنیم همون لحظه از DR/BDR بودن می افتند و دیگر DR/BDR نمیشن روتر دفتر مرکزی که دیفالت پرایورتی اش یک است پس DR میشود.

```cisco

RTR-B1(config)#interface ethernet 1/1
RTR-B1(config-if)#ip ospf priority 0

RTR-B2(config)#inter ethernet 1/2
RTR-B2(config-if)#ip ospf priority 0

RTR-B3(config)#interface ethernet 1/3
RTR-B3(config-if)#ip ospf priority 0


RTR-HQ#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     0   FULL/DROTHER    00:00:34    192.168.10.11   Ethernet1/0
192.168.10.12     0   FULL/DROTHER    00:00:39    192.168.10.12   Ethernet1/0
192.168.10.13     0   FULL/DROTHER    00:00:37    192.168.10.13   Ethernet1/0

```
## Routing Table Verification

```cisco
RTR-B1#sh ip route
Gateway of last resort is not set

      10.0.0.0/24 is subnetted, 1 subnets
O        10.20.30.0 [110/20] via 192.168.10.10, 00:13:28, Ethernet1/1
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.16.0.0/16 is directly connected, Ethernet0/0
L        172.16.1.1/32 is directly connected, Ethernet0/0
O     172.17.0.0/16 [110/20] via 192.168.10.12, 00:13:28, Ethernet1/1
O     172.18.0.0/16 [110/20] via 192.168.10.13, 00:13:28, Ethernet1/1
      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/28 is directly connected, Ethernet1/1
L        192.168.10.11/32 is directly connected, Ethernet1/1

```


** مشکل بزرگی که هست این است که درسته که روترها روت همدیگر را یاد گرفته اند ولی وقتی ما DR/BDR را میاریم وسط NEXT HUB ها حفظ میشوند و روتینگ درست نیست. ** 
### برای اینکه این وضعیت را تغییر دهیم باید برویم سراغ نتورک تایپ هایی که DR/BDR ندارند.

```cisco

RTR-HQ(config)#inter ethernet 1/0
RTR-HQ(config-if)#ip ospf network point-to-multipoint

RTR-B1(config)#inter e1/1
RTR-B1(config-if)#ip ospf network point-to-multipoint

RTR-B2(config)#inter e1/2
RTR-B2(config-if)#ip ospf network point-to-multipoint

RTR-B3(config)#inter e1/3
RTR-B3(config-if)#ip ospf network point-to-multipoint

RTR-HQ#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     0   FULL/  -        00:01:45    192.168.10.11   Ethernet1/0
192.168.10.13     0   FULL/  -        00:01:40    192.168.10.13   Ethernet1/0
192.168.10.12     0   FULL/  -        00:01:47    192.168.10.12   Ethernet1/0

RTR-B1#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.10     0   FULL/  -        00:01:58    192.168.10.10   Ethernet1/1

RTR-B1#sh ip route

      10.0.0.0/24 is subnetted, 1 subnets
O        10.20.30.0 [110/20] via 192.168.10.10, 00:03:41, Ethernet1/1
      172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks
C        172.16.0.0/16 is directly connected, Ethernet0/0
L        172.16.1.1/32 is directly connected, Ethernet0/0
O     172.17.0.0/16 [110/30] via 192.168.10.10, 00:03:31, Ethernet1/1
O     172.18.0.0/16 [110/30] via 192.168.10.10, 00:03:41, Ethernet1/1
      192.168.10.0/24 is variably subnetted, 5 subnets, 2 masks
C        192.168.10.0/28 is directly connected, Ethernet1/1
O        192.168.10.10/32 [110/10] via 192.168.10.10, 00:03:41, Ethernet1/1
L        192.168.10.11/32 is directly connected, Ethernet1/1
O        192.168.10.12/32 [110/20] via 192.168.10.10, 00:03:31, Ethernet1/1
O        192.168.10.13/32 [110/20] via 192.168.10.10, 00:03:41, Ethernet1/1

```
### مشکل NEXT HUB روترها حل شد.

