# Verification RTR-HQ

## Deviec Information

-**Device:** RTR-HQ

-**Location:** RTR-Headquarter

-**Role:** HQ Router (Area 0)

---



## Interface status  Verification

```cisco

RTR-HQ#sh ip os interface tunnel 0
Tunnel0 is up, line protocol is up
  Internet Address 192.168.0.1/24, Area 0, Attached via Network Statement
  Process ID 1, Router ID 192.168.0.1, Network Type BROADCAST, Cost: 1000
  Topology-MTID    Cost    Disabled    Shutdown      Topology Name
        0           1000      no          no            Base
  Transmit Delay is 1 sec, State DR, Priority 1

  
RTR-HQ#sh ip os neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.0.11      0   FULL/DROTHER    00:00:36    192.168.0.11    Tunnel0
192.168.0.22      0   FULL/DROTHER    00:00:35    192.168.0.22    Tunnel0


RTR-HQ#sh ip route

Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 6 subnets, 3 masks
C        10.0.0.0/30 is directly connected, Ethernet1/0
L        10.0.0.2/32 is directly connected, Ethernet1/0
S        10.1.1.0/30 [1/0] via 10.0.0.1
S        10.2.2.0/30 [1/0] via 10.0.0.1
C        10.20.30.0/24 is directly connected, Ethernet0/0
L        10.20.30.1/32 is directly connected, Ethernet0/0
O     172.16.0.0/16 [110/1010] via 192.168.0.11, 00:12:14, Tunnel0
O     172.17.0.0/16 [110/1010] via 192.168.0.22, 00:26:15, Tunnel0
      192.168.0.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.0.0/24 is directly connected, Tunnel0
L        192.168.0.1/32 is directly connected, Tunnel0



```

