# OSPF Layer2 WAN FULL Mesh

## توضیح LAB

### در این LAB ما از یک WAN لایه 2 استفاده کرده ایم در ISP سویچ هایی مثلاً WPLS یا مترواترنت قرار گرفته و این ابر مخابرات WAN لایه 2 است و فول مش بوده و میخواهیم OSPF را هاندازی کنیم و ببینیم کی DR و BDR شده است.
### DR و BDR در داخل اینترفیس های روتر اتفاق می افتد نه کل روتر، یعنی روتر میتواند در یک دستش DR شده باشد و در اون یکی دستش BDR شده و به یک ابر مخابراتی دیگه وصل شده باشد.
### اگر در این ابر مخابرات روترها فول مش همدیگر را می بینند ما در اینجا نیاز به DR و BDR داریم پس میرویم سراغ نتورک تایپ هایی که Boradcast اند.
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
  Transmit Delay is 1 sec, State DROTHER, Priority 1

RTR-B1#sh ip ospf interface ethernet 1/1
Ethernet1/1 is up, line protocol is up
  Internet Address 192.168.10.11/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.11, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State DROTHER, Priority 1


RTR-B2#sh ip ospf interface ethernet 1/2
Ethernet1/2 is up, line protocol is up
  Internet Address 192.168.10.12/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.12, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State BDR, Priority 1

RTR-B3#sh ip ospf interface ethernet 1/3
Ethernet1/3 is up, line protocol is up
  Internet Address 192.168.10.13/28, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.10.13, Network Type BROADCAST, Cost: 10
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           10        no          no            Base
  Transmit Delay is 1 sec, State DR, Priority 1


```

### همانطور که مشاهده می کنید روترRTR-B3 در وضعیت DR قرار گرفته است چون عدد پرایورتی دیفالت هر اینترفیس یک می باشد و مساوی میباشد هر کسی که روتر آیدی اش بزرگتر باشد  



## OSPF Area Design

| روتر    | روتر آیدی | وضعیت |
|--------|--------|---------|
| RTR-HQ | 192.168.10.10| DROTHER |
| RTR-B1 | 192.168.10.11| DROTHER |
| RTR-B2 | 192.168.10.12| BDR |
| RTR-B3 | 192.168.10.13| DR |

**DR با همه در وضعیت Full همسایه می شود** 
```cisco

RTR-B3#sh ip os neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.10     1   FULL/DROTHER    00:00:39    192.168.10.10   Ethernet1/3
192.168.10.11     1   FULL/DROTHER    00:00:35    192.168.10.11   Ethernet1/3
192.168.10.12     1   FULL/BDR        00:00:36    192.168.10.12   Ethernet1/3

RTR-HQ#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     1   2WAY/DROTHER    00:00:36    192.168.10.11   Ethernet1/0
192.168.10.12     1   FULL/BDR        00:00:30    192.168.10.12   Ethernet1/0
192.168.10.13     1   FULL/DR         00:00:35    192.168.10.13   Ethernet1/0

```
** DROther ها با DR و BDR همسایه میشود و با هم همسایه نمی شوند ** 
### برای اینکه این وضعیت را تغییر دهیم و RTR-HQ را DR کنیم اول بای پرایورتی RTR-HQ را افزایش بدهیم بعد روترهایی که وضعیتشان DR بعد است را دستور clear بزنیم تا پرایورتی HQ اعمال شود

```cisco

RTR-HQ(config)#interface ethernet 1/0
RTR-HQ(config-if)#ip ospf priority 10
RTR-HQ(config-if)#exit

RTR-B3#clear ip ospf process
Reset ALL OSPF processes? [no]: yes

RTR-B2#clear ip ospf process
Reset ALL OSPF processes? [no]: yes

RTR-HQ#show ip ospf interface ethernet 1/0
  Transmit Delay is 1 sec, State DR, Priority 10
  
RTR-HQ#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     1   FULL/DROTHER    00:00:37    192.168.10.11   Ethernet1/0
192.168.10.12     1   FULL/DROTHER    00:00:35    192.168.10.12   Ethernet1/0
192.168.10.13     1   FULL/BDR        00:00:34    192.168.10.13   Ethernet1/0

---

## OSPF Cost Calculation

OSPF cost formula (default reference bandwidth = 100 Mbps):

| Tunnel | Link Bandwidth | OSPF Cost | Path Selection |
|--------|----------------|-----------|----------------|
| Tunnel1 (to RTR-HQ-1) | 8 Mbps | 100/8 = 12 | ✅ Preferred (lower cost) |
| Tunnel2 (to RTR-HQ-2) | 4 Mbps | 100/4 = 25 | ⚠️ Standby (higher cost) |

Result: All traffic prefers the 8 Mbps tunnel through RTR-HQ-1. The 4 Mbps tunnel is only used when the primary fails.

---

## Expected Behavior

| Scenario | Traffic Path |
|----------|--------------|
| Both tunnels up | All traffic through Tunnel1 (8 Mbps to RTR-HQ-1) |
| Tunnel1 fails | Traffic automatically switches to Tunnel2 (4 Mbps to RTR-HQ-2) |
| Tunnel1 recovers | Traffic automatically switches back to Tunnel1 |
| Manual cost adjustment | Can force traffic to use specific tunne2 |

---

## Why Traffic Uses the 8 Mbps Tunnel Only

This is called **Active/Standby **or **Primary/Backup** behavior with automatic failover.

---


## Verification Commands

| Command | What to verify |
|---------|----------------|
| `show ip ospf neighbor` | Both tunnels show FULL state |
| `show ip ospf interface tunnel1` | Check cost (should be 12) |
| `show ip ospf interface tunnel2` | Check cost (should be 25) |
| `show ip route ospf` | Routes from HQ show next-hop via Tunnel1 |
| `show ip route` | Confirm only Tunnel1 path is in routing table |

---

## Failover Testing

**To test failover:**
1. Shutdown Tunnel1 on RTR-BRANCH
2. Check routing table - path should now use Tunnel2
3. Bring Tunnel1 back up
4. Routing table should revert to Tunnel1

**Verification after failover:**
```cisco
RTR-BRANCH# show ip route ospf
O 10.1.1.0/24 [110/12] via 172.16.0.2, 00:00:05, Tunnel1
O 10.2.2.0/24 [110/12] via 172.16.0.2, 00:00:05, Tunnel1