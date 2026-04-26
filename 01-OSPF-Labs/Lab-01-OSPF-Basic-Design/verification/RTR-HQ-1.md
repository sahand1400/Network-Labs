\## 1.OSPF Neighbors (`show ip ospf neighbors`)



```cisco

RTR-HQ-1#sh ip ospf neighbor



Neighbor ID     Pri   State           Dead Time   Address         Interface

3.3.3.3           1   FULL/DR         00:00:35    192.168.0.1     Ethernet0/1

2.2.2.2           1   FULL/DR         00:00:39    192.168.0.10    Ethernet0/0

4.4.4.4           1   FULL/DR         00:00:39    192.168.1.2     Ethernet1/1



```

\## 2.OSPF Interface (`show ip ospf interface brief`)



```cisco

RTR-HQ-1#show ip ospf interface brief



Interface    PID   Area            IP Address/Mask    Cost  State Nbrs F/C

Et0/1        1     0               192.168.0.2/30     10    BDR   1/1

Et0/0        1     0               192.168.0.9/30     10    BDR   1/1

Et1/1        1     1               192.168.1.1/30     10    BDR   1/1



```

\## 3.OSPF Interface  (`show ip ospf interface Ethernet0/1`)



```cisco
RTR-HQ-1#show ip ospf interface Ethernet0/1



Ethernet0/1 is up, line protocol is up

&nbsp; Internet Address 192.168.0.2/30, Area 0, Attached via Network Statement

&nbsp; Process ID 1, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 10

&nbsp; Topology-MTID    Cost    Disabled    Shutdown      Topology Name

&nbsp;       0           10        no          no            Base

&nbsp; Transmit Delay is 1 sec, State BDR, Priority 1

&nbsp; Designated Router (ID) 3.3.3.3, Interface address 192.168.0.1

&nbsp; Backup Designated router (ID) 1.1.1.1, Interface address 192.168.0.2

&nbsp; Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5

&nbsp;   oob-resync timeout 40

&nbsp;   Hello due in 00:00:01

&nbsp; Supports Link-local Signaling (LLS)

&nbsp; Cisco NSF helper support enabled

&nbsp; IETF NSF helper support enabled

&nbsp; Index 2/2, flood queue length 0

&nbsp; Next 0x0(0)/0x0(0)

&nbsp; Last flood scan length is 1, maximum is 4

&nbsp; Last flood scan time is 0 msec, maximum is 0 msec

&nbsp; Neighbor Count is 1, Adjacent neighbor count is 1

&nbsp;   Adjacent with neighbor 3.3.3.3  (Designated Router)

&nbsp; Suppress hello for 0 neighbor(s)



```

