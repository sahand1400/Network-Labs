# Verification RTR-HQ

## Deviec Information

-**Device:** RTR-HQ

-**Location:** RTR-Headquarter

-**Role:** HQ Router (Area 0)

---



## Interface status  Verification

```cisco

RTR-HQ#show ip ospf interface ethernet 1/0
  Transmit Delay is 1 sec, State DR, Priority 10
  
RTR-HQ#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.10.11     0   FULL/  -        00:01:45    192.168.10.11   Ethernet1/0
192.168.10.13     0   FULL/  -        00:01:40    192.168.10.13   Ethernet1/0
192.168.10.12     0   FULL/  -        00:01:47    192.168.10.12   Ethernet1/0

RTR-B1#sh ip route

Gateway of last resort is not set

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

