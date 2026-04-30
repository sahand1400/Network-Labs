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
192.168.10.11     1   FULL/DROTHER    00:00:37    192.168.10.11   Ethernet1/0
192.168.10.12     1   FULL/DROTHER    00:00:35    192.168.10.12   Ethernet1/0
192.168.10.13     1   FULL/BDR        00:00:34    192.168.10.13   Ethernet1/0

```

