# Verification RTR-Branch

## Deviec Information

-**Device:** RTR-Branch

-**Location:** RTR-Branch

-**Role:** Branch Router (Area 1)

---



### همانطور که در توپولوژی مشاهده می شود مقدار پهنای باند تانل1 و تانل 2 مشخص شده است میخواهیم ترافیک به سمت سابنت های سرورها از تانل 1 که  8 مگ  است برود و اگر تانل قطع شد از تانل 2 که 4 مگ است  برود فیل اور 

## Routing Table Verification

```cisco

RTR-B2#show ip route  

O IA     10.1.1.0/24 [110/23] via 192.168.1.1, 00:00:50, Tunnel1
O IA     10.2.2.0/24 [110/23] via 192.168.1.1, 00:00:50, Tunnel1


```

### برای تست لینک 8 مگ راقطع میکنیم و مجدداً تست میکنیم 

```cisco

RTR-Branch(config)#interface tunnel 1
RTR-Branch(config-if)#shutdown

RTR-B1#sh ip route

O IA     10.1.1.0/24 [110/36] via 192.168.2.1, 00:00:59, Tunnel2
O IA     10.2.2.0/24 [110/36] via 192.168.2.1, 00:00:59, Tunnel2


```

###  ترافیک به سمت سابنت های سرورها از تانل2 که 4 مگ است میرود.

