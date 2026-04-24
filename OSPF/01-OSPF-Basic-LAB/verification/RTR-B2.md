# Verification RTR-B2


## Deviec Information

-**Device:** RTR-B2

-**Location:** Branch2

-**Role:** Branch Router (Area 1)



---



## Routing Table Verification



```cisco

RTR-B2#show ip route  

O IA     10.1.1.0 [110/75] via 192.168.4.1, 00:11:23, Serial2/2

                  [110/75] via 192.168.3.1, 00:11:23, Serial2/1

O IA     10.2.2.0 [110/75] via 192.168.4.1, 00:11:23, Serial2/2

                  [110/75] via 192.168.3.1, 00:11:23, Serial2/1


O   172.16.0.0/16 [110/84] via 192.168.4.1, 00:11:13, Serial2/2

                  [110/84] via 192.168.3.1, 00:11:13, Serial2/1


```

## Load Balancing is Active for each destination subnet, there are two equal-cost paths:

```cisco

RTR-B2#show interfaces serial 2/1

MTU 1500 bytes, BW 1544 Kbit/sec, DLY 20000 usec,

RTR-B2#show ip ospf interface serial 2/1

Process ID 1, Router ID 5.5.5.5, Network Type POINT_TO_POINT, ## Cost: 64 

```



