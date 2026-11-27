# Enterprise 3-Tier Network Infrastructure

## 📌 Project Overview
This project simulates a secure, three-tier enterprise network for a multi-floor county courthouse using Cisco Packet Tracer. \
It demonstrates hierarchical network architecture, network segmentation, redundancy, RADIUS, QoS, EtherChannel, NAT, DHCP, DNS, and network device hardening.


### Key Features & Technologies
* **Architecture:** Three-tier (Edge, Core, Distribution/Access)
* **Routing Protocols:** OSPF, VRRP, BGP
* **Segmentation:** 15+ Departmental VLANs with VLSM subnetting
* **Services:** Centralized DHCP , DNS, AAA/RADIUS Authentication, Web Server
* **Security & Control:** Port security, Native VLAN isolation, Passive interfaces, SSH management, SNMP, LLDP, MAC address filtering

---

## 📐 Network Topology
![Logical Topology](docs/Logical_Topology.png)

### Device Roles
* **`Edge-Router/2, Edge-Firewall/2`:** Handles WAN handoff to both ISPs, NAT, and outer firewall boundary.
* **`L3Sw-Core`, `L3Sw-Core2`:** Act as the backbone, facilitating North-South traffic and server connections.
* **`Sw/2-Basement`, `Sw/2-Floor1`, `Sw/2-Floor2`, `Sw/2-Floor3`:** Access switches providing connectivity for workstations, IoT, and WAP.
* **`CyberObserver`:**
* **`NOC workstation`:**

---

## 📊 VLAN & IP Subnetting Scheme

| VLAN ID | Department           | Subnet Address | Mask | CIDR | Usable Hosts | Usable Host Range | Default Gateway |
| :---:  | :---                  | :---           | :--- | :---: | :---:       | :---              | :--- |
| **10** | Emergency Management  | 10.10.10.0 | 255.255.255.224 | /27 | 30 | 10.10.10.1 - 10.10.10.30 | 10.10.10.1 |
| **20** | Assessor's Office     | 10.10.20.0 | 255.255.255.224 | /27 | 30 | 10.10.20.1 - 10.10.20.30 | 10.10.20.1 |
| **21** | Collector's Office    | 10.10.21.0 | 255.255.255.224 | /27 | 30 | 10.10.21.1 - 10.10.21.30 | 10.10.21.1 |
| **22** | Coroner's Office      | 10.10.22.0 | 255.255.255.240 | /28 | 14 | 10.10.22.1 - 10.10.22.14 | 10.10.22.1 |
| **23** | County Clerk's Office | 10.10.23.0 | 255.255.255.224 | /27 | 30 | 10.10.23.1 - 10.10.23.30 | 10.10.23.1 |
| **24** | Janitorial            | 10.10.24.0 | 255.255.255.240 | /28 | 14 | 10.10.24.1 - 10.10.24.14 | 10.10.24.1 |
| **30** | Circuit Clerk         | 10.10.30.0 | 255.255.255.224 | /27 | 30 | 10.10.30.1 - 10.10.30.30 | 10.10.30.1 |
| **31** | Juvenile Office       | 10.10.31.0 | 255.255.255.224 | /27 | 30 | 10.10.31.1 - 10.10.31.30 | 10.10.31.1 |
| **32** | Public Administrator  | 10.10.32.0 | 255.255.255.240 | /28 | 14 | 10.10.32.1 - 10.10.32.14 | 10.10.32.1 |
| **40** | Judge Chambers        | 10.10.40.0 | 255.255.255.240 | /28 | 14 | 10.10.40.1 - 10.10.40.14 | 10.10.40.1 |
| **41** | Judge Secretaries     | 10.10.41.0 | 255.255.255.240 | /28 | 14 | 10.10.41.1 - 10.10.41.14 | 10.10.41.1 |
| **42** | Courtrooms            | 10.10.42.0 | 255.255.255.224 | /27 | 30 | 10.10.42.1 - 10.10.42.30 | 10.10.42.1 |
| **43** | Prosecuting Attorney  | 10.10.43.0 | 255.255.255.224 | /27 | 30 | 10.10.43.1 - 10.10.43.30 | 10.10.43.1 |
| **50** | Servers               | 10.10.50.0 | 255.255.255.224 | /27 | 30 | 10.10.50.1 - 10.10.50.30 | 10.10.50.1 |
| **60** | Guest Wi-Fi           | 192.168.0.0 | 255.255.254.0  | /23 | 510 | 192.168.0.1 - 192.168.1.254 | 192.168.0.1 |
| **70** | IoT                   | 10.10.70.0 | 255.255.255.0   | /24 | 255 | 10.10.70.1 - 10.10.70.254 | 10.10.70.1 |
| **97** | Printers              | 10.10.97.0 | 255.255.255.224 | /27 | 30 | 10.10.97.1 - 10.10.97.30 | 10.10.97.1 |
| **98** | Voice VLAN (VoIP)     | 10.10.98.0 | 255.255.255.0   | /24 | 254 | 10.10.98.1 - 10.10.98.254 | 10.10.98.1 |
| **99** | Management VLAN       | 10.10.99.0 | 255.255.255.224 | /27 | 30 | 10.10.99.1 - 10.10.99.30 | 10.10.99.1 |

## 🛠️ Configuration & Implementation Highlights

### 1. Feature
description
```ciscolike
command
command
command
```
### 3. Security Implementation
* **Port Security:** Enabled on access ports across floor switches to restrict MAC address learning and prevent unauthorized device attachments.
* **Management Access:** Telnet is disabled globally in favor of encrypted SSH access over the Management VLAN (`VLAN 99`).
* **Guest Isolation:** Access Control Lists (ACLs) are deployed at the edge to ensure the **Guest Wi-Fi (VLAN 60)** has internet access only, completely blocking lateral movement into county department subnets and the Server Farm.

### 4. Link Aggregation (EtherChannel)
Port channels are configured between the core switches using LACP to provide redundancy and aggregate bandwidth.
```ciscolike
interface range GigabitEthernet0/1 - 2
 channel-group 1 mode active
!
interface Port-channel 1
 switchport mode trunk
 description Core_Uplink_Aggregation
```

## 💭 Final thoughts & Considerations

### 1. Access switch redundancy
When looking at the topology, using a single access switch can take out an entire floor, and can also run short on ports for every wall mount. I originally wanted to use Cisco StackWise so two switches would act as one logical switch while splitting endpoints across both physical units to limit the failure domain. Because Packet Tracer doesn't support StackWise, I used separate access switches with redundant uplinks/STP instead.
