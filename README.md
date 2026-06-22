# 🏢 Smart Co-Working Space Network Architecture

**Course:** CSC61604 Computer Networks
**Tools Used:** Cisco Packet Tracer, Cisco IOS

## 📋 Project Overview
This project involves the design and configuration of a comprehensive, two-story smart co-working space network simulated using Cisco Packet Tracer. The network architecture supports a diverse range of users, including wired administrative staff, wireless guests, private office tenants, and IoT-based security devices. 

The primary goal of this implementation is to provide structured connectivity, logical isolation via VLANs, and strict access control, while simulating a modern smart workplace environment that requires external internet/server access.

---

## 🏗️ Network Architecture & Design

The infrastructure utilizes a hierarchical logical design featuring access switches, a central core switch, and a main gateway router to maintain scalability and organized traffic flow.

### Hardware Components
| Component | Model / Type | Purpose |
| :--- | :--- | :--- |
| **Main Router** | Cisco 2911 | Inter-VLAN routing and connection to ISP |
| **ISP Router** | Cisco 2911 | Simulated external/ISP path to remote server |
| **Core Switch** | Cisco 2960-24TT | Central aggregation switch connecting both floors |
| **Access Switches** | Cisco 2960-24TT | Access-layer switches for ground and first-floor devices |
| **Access Points** | AccessPoint-PT | Wireless connectivity for staff, guests, and hot-desk users |
| **IoT Gateway** | Home Gateway | Centralized IoT registration and management |

---

## 🗄️ VLAN Segmentation & IP Addressing

The network utilizes a private Class A addressing scheme (`10.10.0.0/16`), subnetted into four Class C blocks (`/24`) to provide a scalable address space.

| VLAN ID | Name | Subnet / Mask | Default Gateway | Assigned Users/Devices |
| :--- | :--- | :--- | :--- | :--- |
| **10** | STAFF | `10.10.10.0/24` | `10.10.10.1` | Reception PCs, Admin PC, and Admin Printers |
| **20** | OFFICE | `10.10.20.0/24` | `10.10.20.1` | Private Office Tenants and Hot-desk Freelancers |
| **30** | GUEST | `10.10.30.0/24` | `10.10.30.1` | Public Wi-Fi users in the Communal Lounge |
| **40** | IOT | `10.10.40.0/24` | `10.10.40.1` | Smart Cameras, Smart Locks, LEDs, and Projectors |

*   **Static Reservations:** The first 20 addresses of each subnet (`.1` through `.20`) are reserved for critical infrastructure, such as default gateways, access points, and network printers.
*   **DHCP Services:** Dynamic IP allocation is handled by the main router, with exclusions applied to prevent IP conflicts with the statically assigned infrastructure.

---

## 🔒 Security & Routing Protocols

### 1. Inter-VLAN Routing
*   Implemented **Router-on-a-Stick (ROAS)** on the core router using sub-interfaces to facilitate secure communication between isolated subnets.
*   IEEE 802.1Q trunking is configured on the connections between the access switches, core switch, and main router.

### 2. Access Control Lists (ACLs)
Extended ACLs are applied to enforce strict network policies across the VLANs:
*   **Guest Network (VLAN 30):** Permitted internet access but explicitly denied access to any internal devices, administration PCs, or IoT hardware.
*   **Tenant Network (VLAN 20):** Permitted internet and shared printer access, but blocked from accessing administrative networks or IoT infrastructure.
*   **Admin Network (VLAN 10):** Granted full unrestricted access to all devices, the internet, IP cameras, and smart doors.
*   **DHCP Prioritization:** UDP ports 67 (bootps) and 68 (bootpc) are permitted at the very top of the OFFICE and GUEST ACLs to ensure DHCP requests are processed before restrictive denial rules are applied.

### 3. Network Address Translation (NAT)
*   Configured **Port Address Translation (PAT / NAT Overload)** to allow multiple private IP addresses within the internal network to map to a single public IP address when accessing the external ISP server (`203.0.113.88`).

---

## 🛠️ Challenges & Troubleshooting

*   **IoT Device Interference:** Initial setups experienced cross-floor wireless interference causing smart doors and webcams to lose connections. This was resolved by physically mapping distinct SSIDs to the Home Gateways (`IOT-FF` for the first floor and `IOT-GF` for the ground floor) and manually re-associating the devices.
*   **DHCP Allocation Failures:** Strict Extended ACL implementations inadvertently blocked DHCP broadcast packets, leaving devices unresponsive. The ACL rules were restructured to prioritize the DHCP DORA process, successfully resolving the IP allocation failures.

---
