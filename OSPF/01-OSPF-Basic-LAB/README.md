# OSPF Basic Configuration Lab 1 - Multi-Area Redundant Network



## Lab Overview



This lab demonstrates a ** multi-area OSPF ** design with redundant WAN connections between headquarters and branch offices. The goal is to implement path selection based on bandwidth and configure load balancing.



---



## Lab Objectives



** Branch 1 (RTR-B1) **

- Redundancy Type: Active/Standby

- Primary Link: 8 Mbps WAN

- Backup Link: 4 Mbps WAN

- Traffic Behavior: Traffic uses primary link (lower cost = higher bandwidth)



** Branch 2 (RTR-B2) **

- Redundancy Type: Load Balancing

- Primary Link: 2 Mbps Serial

- Backup Link: 2 Mbps Serial

- Traffic Behavior: Traffic distributes across both links (equal cost)



---



## OSPF Design



- ** Area 0 (Backbone): ** RTR-HQ-1, RTR-HQ-2, SW-Core

- ** Area 1: ** RTR-B1, RTR-B2

- ** ABR Routers: ** RTR-HQ-1, RTR-HQ-2



---



## Path Selection Logic



OSPF uses ** cost ** to determine the best path. Cost is calculated based on interface bandwidth. A lower cost means a better path.



** Branch 1: ** The 8 Mbps link has a lower cost than the 4 Mbps link, so all traffic flows through the primary link. If the primary link fails, traffic automatically switches to the backup link.



** Branch 2: ** Both serial links have the same bandwidth (2 Mbps), so they have equal cost. OSPF performs **load balancing** across both links.



---



## Expected Behavior



** When primary link on Branch 1 is up: ** All traffic uses the 8 Mbps path.



** When primary link on Branch 1 fails: ** Traffic automatically switches to the 4 Mbps backup path.



** When both links on Branch 2 are up: ** Traffic load balances across both serial links.



** When one link on Branch 2 fails: ** Traffic continues via the remaining link.



---



## Lab Files



- ** configs/ ** → Running configurations for all routers

- ** verification/ ** → Command outputs with analysis

- ** eve-ng-export/ ** → EVE-NG lab export file

- ** troubleshooting/ ** → Common issues and solutions

