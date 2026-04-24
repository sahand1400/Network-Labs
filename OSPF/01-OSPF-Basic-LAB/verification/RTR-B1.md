# Verification RTR-B1

## Deviec Information

-**Device:** RTR-B1

-**Location:** Branch1

-**Role:** Branch Router (Area 1)

---
##با استفاده از دستور بندوید زیر اینارفیس ها مقدار پهنای باند پورت ها را مشخص کرده ایم

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
##برای تست لینک 8 مگ راقطع میکنیم و مجدداً تست میکنیم

```cisco

SW1-WAN-HQ(config)#interface e0/0
SW1-WAN-HQ(config-if)#shut
SW1-WAN-HQ(config-if)#shutdown

RTR-B1#sh ip route

O IA     10.1.1.0 [110/36] via 192.168.2.1, 00:00:13, Ethernet1/2
O IA     10.2.2.0 [110/36] via 192.168.2.1, 00:00:13, Ethernet1/2

```
##  ترافیک به سمت سابنت های سرورها از پورت 4 مگ میرود این تغییر پهنای باند را در دو سر لینک انجام میدهیم 

#میتوان مستقیماً  متریک را در اینترفیس ها تنظیم کرد 
## ابتدا پهنای باندی که به صورت دستی رو ی اینترفیس ها تنطیم کرده ایم را برمیداریم

```cisco

RTR-B1(config)#interface ethernet 1/1
RTR-B1(config-if)#no bandwidth
RTR-B1(config-if)#ip ospf cost 100

RTR-B1(config)#interface ethernet 1/2
RTR-B1(config-if)#no bandwidth
RTR-B1(config-if)#ip ospf cost 200

```

##پهنای باند پیشفرض پورت های اترنت 10 مگ می باشد 
#با تنظیم دستی متریک انتخاب مسیر انجام گرفته و برای سابت های سرور ها مسیر با متریک کمتر انتخاب شده است

```cisco

RTR-B1#sh ip ospf interface ethernet 1/1
 Ethernet1/1 is up, line protocol is up
  Internet Address 192.168.1.2/30, Area 1, Attached via Interface Enable
  Process ID 1, Router ID 4.4.4.4, Network Type BROADCAST, Cost: 100

RTR-B1#sh ip ospf interface ethernet 1/2
Ethernet1/2 is up, line protocol is up
  Internet Address 192.168.2.2/30, Area 1, Attached via Interface Enable
  Process ID 1, Router ID 4.4.4.4, Network Type BROADCAST, Cost: 200






