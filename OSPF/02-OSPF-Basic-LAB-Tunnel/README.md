# OSPF over GRE Tunnel - Asymmetric Bandwidth Design

## Lab Overview

This lab demonstrates OSPF over GRE tunnels between a branch router and two headquarters routers with different bandwidth links. The branch has two tunnels terminating on two different HQ routers with asymmetric speeds.

---

## Network Design

**Headquarters (Area 0)**
- RTR-HQ-1
- RTR-HQ-2

**Branch (Area 1)**
- RTR-BRANCH

**Tunnels from Branch to HQ**

| Tunnel | Destination | Link Bandwidth | Role |
|--------|-------------|----------------|------|
| Tunnel1 | RTR-HQ-1 | 8 Mbps | Primary (Higher bandwidth) |
| Tunnel2 | RTR-HQ-2 | 4 Mbps | Backup (Lower bandwidth) |

---

## OSPF Area Design

| Location | Area | Routers |
|----------|------|---------|
| Headquarters | Area 0 (Backbone) | RTR-HQ-1, RTR-HQ-2 |
| Branch | Area 1 | RTR-BRANCH |

**ABR Routers:** RTR-HQ-1 and RTR-HQ-2 (connected to both Area 0 and Area 1 via tunnels)

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