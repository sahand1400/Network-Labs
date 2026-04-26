# Verification RTR-B1

## Deviec Information

-**Device:** RTR-B1

-**Location:** Branch1

-**Role:** Branch Router (Area 1)

---
## با استفاده از دستور بندوید زیر اینترفیس ها مقدار پهنای باند پورت ها را مشخص کرده ایم

```cisco
RTR-B1#show interfaces ethernet 1/1  

MTU 1500 bytes,<h2> BW 8000 Kbit/sec </h2>, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255

RTR-B1#show interfaces ethernet 1/2

MTU 1500 bytes, BW 4000 Kbit/sec, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255


```
##همانطور که مشاهده می شود مقدار پهنای باند مشخص شده است چون میخواهیم ترافیک به سمت سابنت های سرورها از پورت 8 مگ برود و اگر لینک قطع شد از پورت 4 مگ برود فیل اور 

## Routing Table Verification

```cisco

RTR-B2#show ip route  

O IA     10.1.1.0 [110/23] via 192.168.1.1, 00:08:09, Ethernet1/1
O IA     10.2.2.0 [110/23] via 192.168.1.1, 00:08:09, Ethernet1/1

```
## برای تست لینک 8 مگ راقطع میکنیم و مجدداً تست میکنیم 

```cisco

SW1-WAN-HQ(config)#interface e0/0
SW1-WAN-HQ(config-if)#shut
SW1-WAN-HQ(config-if)#shutdown

RTR-B1#sh ip route

O IA     10.1.1.0 [110/36] via 192.168.2.1, 00:00:13, Ethernet1/2
O IA     10.2.2.0 [110/36] via 192.168.2.1, 00:00:13, Ethernet1/2

```
##  ترافیک به سمت سابنت های سرورها از پورت 4 مگ میرود این تغییر پهنای باند را در دو سر لینک انجام میدهیم 

