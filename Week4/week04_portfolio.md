# 📄 Week 04 – Routing Tables & OSPF Dynamic Routing

## 👤 Student Details
- **Name:** Tabib Al Adib  
- **Student ID:** 12307888  
- **Unit:** COIT20261 – Network Services and Automation  
- **Week:** 04  

---

# Task 1: Routing Tables & Network Testing

## Objective
- Configure a multi-subnet network  
- View routing tables  
- Enable/disable IP forwarding  
- Test communication across subnets  

---

## Network Topology
![Network Topology](View-Routes-12307888-network.png)

---

##  IP Addressing Scheme

### Subnet 1: 10.10.1.0/24
| Device | IP Address |
|--------|-----------|
| Host1 | 10.10.1.88 |
| Host2 | 10.10.1.89 |
| Router (eth0) | 10.10.1.1 |

### Subnet 2: 10.10.2.0/24
| Device | IP Address |
|--------|-----------|
| Host3 | 10.10.2.88 |
| Router (eth1) | 10.10.2.1 |

---

## Routing Tables

### Host1

- default via 10.10.1.1 dev eth0
- 10.10.1.0/24 dev eth0 scope link src 10.10.1.88
  
![Host 1](IP-Address-Routes-12307888-host1.png)


### Host2
- default via 10.10.1.1 dev eth0
- 10.10.1.0/24 dev eth0 scope link src 10.10.1.89
  
![Host 2](IP-Address-Routes-12307888-host2.png)

### Host3
- default via 10.10.2.1 dev eth0
- 10.10.2.0/24 dev eth0 scope link src 10.10.2.88

![Host 3](IP-Address-Routes-12307888-host3.png)

### Router
- 10.10.1.0/24 dev eth0 scope link src 10.10.1.1
- 10.10.2.0/24 dev eth1 scope link src 10.10.2.1
  
![Router](IP-Address-Routes-12307888-router.png)

### Forwarding Status

| Device | IP Forwarding |
|--------|--------------|
| Hosts  | Disabled (0) |
| Router | Enabled (1)  |

### Ping Test
ping 10.10.1.88

#### Result:
- 0% packet loss
- TTL = 63 → Passed through 1 router
![Ping Test](View-Routes-12307888-ping.png)

## Reflection (Task 1)

This task demonstrated how routing works between subnets. I learned that hosts require a default gateway to communicate outside their network, and routers must have IP forwarding enabled to route packets.

---


# Task 2: Dynamic Routing with OSPF
## Objective
- Observe OSPF dynamic routing
- Analyse routing tables
- Identify neighbour routers
- Observe path changes using traceroute

## OSPF Network Topology

![Network Diagram](OSPF-Basics-12307888-network.png)

## OSPF Neighbour Routers (FRR1)

| Neighbour Router | IP Address | Interface |
|------------------|-----------|-----------|
| FRR2             | 10.10.2.2 | eth1      |
| FRR3             | 10.10.3.3 | eth2      |

![OSPF-Basics-neigbour-routers](OSPF-Basics-neigbour-routers.png)

## Routing Tables (OSPF)
FRR1 – OSPF Routes
![OSPF-Basics-IP-OSPF-route-FRR-1](OSPF-Basics-IP-OSPF-route-FRR-1.png)

FRR1 – Full Routing Table

![OSPF-Basics-IP-route-FRR-1](OSPF-Basics-IP-route-FRR-1.png)

FRR2 – Full Routing Table

![OSPF-Basics-IP-route-FRR-](OSPF-Basics-IP-route-FRR-2.png)


## Routing Summary Table

| Destination Network | Next Hop      |
| ------------------- | ------------- |
| 10.10.1.0/24        | Direct        |
| 10.10.2.0/24        | Direct        |
| 10.10.3.0/24        | Direct        |
| 10.10.4.0/24        | via 10.10.2.2 |
| 10.10.5.0/24        | via 10.10.3.3 |
| 10.10.6.0/24        | via 10.10.2.2 |

---

## Traceroute Testing
### Before Link Failure
traceroute 10.10.6.102

### Path Observed: 
#### Host1 → FRR1 → FRR2 → FRR4 → Host2  

- 10.10.1.1
- 10.10.3.3
- 10.10.4.4
- 10.10.6.102


![OSPF-Basics-traceroute-before](OSPF-Basics-traceroute-before.png)

---

### After Link Failure (A NETem node was stopped to simulate link failure)
traceroute 10.10.6.102

### New Path:
#### Host1 → FRR1 → FRR3 → FRR4 → Host2

- 10.10.1.1
- 10.10.2.2
- 10.10.5.4
- 10.10.6.102

![OSPF-Basics-traceroute-after](OSPF-Basics-traceroute-after.png)

## Observation

Before the link failure, packets followed the shortest path through FRR2. After disabling the link, OSPF dynamically recalculated the routes and redirected traffic through FRR3. This demonstrates OSPF’s ability to automatically adapt to network changes and maintain connectivity without manual intervention.

---

## Reflection (Task 2)

This task demonstrated how OSPF dynamically updates routing tables when network conditions change. When a link failed, OSPF automatically recalculated the best path without manual intervention.

I learned that:
- OSPF uses metrics to choose the best path
- Routing updates happen automatically
- Network reliability improves with dynamic routing
