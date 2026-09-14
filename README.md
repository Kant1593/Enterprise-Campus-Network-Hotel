# Enterprise-Campus-Network-Hotel
---

### Project Objectives

The primary objective of the **Vic Modern Hotel Network Infrastructure Project** is to design, implement, and validate a highly available, secure, and scalable enterprise campus network for a three-story commercial facility. The technical goals include:

* **High Availability & Fault Tolerance:** Eliminate single points of failure across the WAN Edge and inter-router backbone by deploying Layer 3 First Hop Redundancy (HSRP), multi-homed ISP routing with floating static failover, LACP EtherChannel trunk aggregation, and dynamic OSPF convergence.
* **Network Segmentation & Scalability:** Establish a hierarchical Layer 2 and Layer 3 architecture utilizing IEEE 802.1Q Router-on-a-Stick trunking to cleanly segment departmental traffic and guest services into dedicated VLANs.
* **Zero-Trust Wireless & BYOD Isolation:** Enforce strict policy-based access control ensuring guest and staff Wi-Fi endpoints obtain dynamic addressing, retain Internet access, and are completely quarantined from internal enterprise services.
* **Defense-in-Depth Security & Hardening:** Secure the physical and logical layers against unauthorized access via 802.1D Spanning Tree guards, MAC-based port security, Blackhole VLAN containment, VTY access restriction, password encryption, and stateful Inter-VLAN Extended ACLs.
* **Automated Network Services:** Provide localized multi-pool DHCP distribution and secure out-of-band management via SSHv2 restricted to authorized administration endpoints.

---

### Network Scenario & Technical Requirements

Vic Modern Hotel operates out of a three-story commercial building requiring a modernized, enterprise-grade network infrastructure. The physical layout comprises three operational departments on the 1st floor (Reception, Store, and Logistics), three administrative and business departments on the 2nd floor (Finance, Human Resources, and Sales/Marketing), and executive/technical management on the 3rd floor (IT Department and Executive Admin). The centralized Data Center/Server Room is hosted within the IT Department on the 3rd floor.

To fulfill operational, resilience, and security standards, the network implementation incorporates the following specifications:

#### 1. Physical Topology & Hardware Distribution

* **Centralized Core/Distribution Routers:** Three dedicated routers (R1 for 1st Floor, R2 for 2nd Floor, R3 for 3rd Floor) are consolidated within the 3rd-floor Server Room.
* **Inter-Router Backbone:** Routers connect in a full-mesh point-to-point topology using Serial DCE cabling to ensure redundant pathing within the core.
* **Access Layer Infrastructure:** Each floor hosts a dedicated Access Switch serving departmental hosts, network printers, and ceiling-mounted Wireless Access Points (WAPs).
* **Dual Edge Transit Infrastructure:** The WAN edge features redundant Layer 2 Edge/Transit Switches interconnecting the perimeter routers to primary and secondary ISP gateways.

#### 2. Link Aggregation & Layer 2 Resiliency

* **Dual Transit Switch Interconnect:** Redundant physical links between the Edge Switches are aggregated into an active **IEEE 802.3ad LACP EtherChannel Trunk**, doubling backplane throughput and eliminating transit switching bottlenecks.
* **Trunking Architecture:** Uplinks between floor switches and their respective distribution routers operate as **802.1Q trunks (Router-on-a-Stick)** to carry all departmental and wireless VLAN tags over minimal physical cabling.
* **Spanning Tree Enhancements:** All edge/access ports directly interfacing with end stations and WAPs are configured with `Spanning-Tree PortFast` and `BPDU Guard` to prevent spanning-tree renegotiation attacks and loops.
* **Layer 2 Quarantine & VLAN Hardening:**
* Default Native VLAN 1 is stripped from all trunk links and mapped to an isolated Blackhole/Parking VLAN.
* All unused switch interfaces are administratively shut down and assigned to the unrouted Blackhole VLAN.



#### 3. Addressing & Dynamic Service Distribution

* **Hierarchical IP Allocation:** The network engineer must design an optimized private IPv4 scheme (RFC 1918) assigning distinct subnets to corporate departmental VLANs, while allocating a completely separate private address space for wireless guest/BYOD pools to simplify perimeter policy enforcement.
* **Core Point-to-Point Links:** Inter-router serial links must be sub-netted using point-to-point `/30` prefixes to conserve address space.
* **Distributed DHCP Architecture:** Each floor router operates as an independent multi-pool DHCP server, dynamically delivering IP leases, subnet masks, default gateway parameters, and DNS addresses to its respective departmental and wireless endpoints.
* **Departmental Endpoints:** Every corporate department maintains desktop workstations and a dedicated Network Printer.

#### 4. Interior Dynamic Routing (OSPF)

* **Single-Area Core:** All distribution routers participate in **OSPF Process 1 within Backbone Area 0** across internal serial links and sub-interfaces.
* **Default-Route Metric Tuning:**
* Active WAN Router (R3) advertises an OSPF default route with a lower metric to serve as the preferred path for all outbound hotel traffic.
* Standby WAN Router (R2) advertises an OSPF default route with an elevated metric to remain an idle, deterministic failover path.



#### 5. WAN Edge, Gateway Redundancy & NAT/PAT

* **First Hop Redundancy (HSRP):** The dual perimeter routers (R2 and R3) interface with the Edge Transit segment running HSRP:
* R3 functions as the **Active Virtual Router** with elevated priority and preemption enabled.
* R2 functions as the **Standby Router**.
* Both share a single Virtual IP (VIP) designated as the gateway towards the ISP domain.


* **Multi-Homed ISP Failover (Floating Static Routing):** Both perimeter routers maintain a primary default static route targeting the Primary ISP, coupled with a higher Administrative Distance (Floating Static Route) targeting the Secondary/Backup ISP.
* **Dynamic PAT (NAT Overload):** Both perimeter routers implement Port Address Translation (PAT) to overload internal corporate subnets and guest wireless ranges across their public WAN interfaces.
* **ISP Reverse Routing:** ISP gateway routers maintain static aggregate return routes directing internal hotel traffic back to the HSRP Virtual IP.

#### 6. Security Policies, ACLs & Device Hardening

* **Zero-Trust Wi-Fi Policy (Extended ACL):** Wireless client traffic is strictly filtered at the sub-interface level to permit essential network services (DHCP and DNS) and outbound public Internet transit, while explicitly dropping (`deny`) all lateral communication towards internal enterprise VLANs and printer resources.
* **Inter-VLAN Departmental Isolation:** Extended ACLs prevent non-privileged departments (e.g., Reception and Store) from establishing connections to restricted administrative and accounting databases (Finance and Executive Admin).
* **Port Security on Critical Access Ports:** The IT Department switch strictly enforces MAC-based Port Security on port `Fa0/1` dedicated to the administration machine (`Test-PC`):
* Employs sticky MAC learning.
* Enforces a violation mode of `shutdown` upon encountering unauthorized hardware.


* **Secure Remote Management (SSHv2):**
* Telnet is permanently disabled on all routing and switching appliances in favor of SSHv2.
* Management Plane ACLs bound to `line vty` restrict remote administrative login privileges exclusively to the `Test-PC` / IT management subnet.


* **System Hardening:** All network appliances enforce universal password hashing (`service password-encryption`), anti-brute-force authentication controls (`login block-for`), and formal legal login warning banners (`banner motd`).

---
# Cisco Packet Tracer
สามารถโหลดไฟล์ Cisco Packet Tracer (.pkt) ได้ที่ [Click here to download](https://drive.google.com/file/d/1CtQH33iUcUt0y91gwiHrdgJ_3EvV5zOz/view?usp=sharing)

รูปภาพ topology ใน Cisco Packet Tracer

---
# Topology
สามารถเข้ามาดู Topology ได้ที่ [Click Here View](https://drive.google.com/file/d/1CtQH33iUcUt0y91gwiHrdgJ_3EvV5zOz/view?usp=sharing)

รูปภาพ logical network topology จาก link ข้างต้น
<img width="5848" height="5288" alt="image" src="https://github.com/user-attachments/assets/f027497f-c134-4d9b-8044-1bc7a6bc31a2" />

---
# IP Addressing Table
| VLAN ID    | Network Address |  Default Gateway |  Usable Range |
| :---: | :---: | :---: | :---: |
| 10    | 192.168.1.0/24    | 192.168.1.1    | 192.168.1.2-192.168.1.254    |
| 20    | 192.168.2.0/24    | 192.168.2.1    | 192.168.2.2-192.168.2.254    |
| 30    | 192.168.3.0/24    | 192.168.3.1    | 192.168.3.2-192.168.3.254    |
| 40    | 192.168.4.0/24    | 192.168.4.1    | 192.168.4.2-192.168.4.254    |
| 50    | 192.168.5.0/24    | 192.168.5.1    | 192.168.5.2-192.168.5.254    |
| 60    | 192.168.6.0/24    | 192.168.6.1    | 192.168.6.2-192.168.6.254    |
| 70    | 192.168.7.0/24    | 192.168.7.1    | 192.168.7.2-192.168.7.254    |
| 80    | 192.168.8.0/24    | 192.168.8.1    | 192.168.8.2-192.168.8.254    |
| 91    | 172.16.10.0/23    | 172.16.10.1    | 172.16.10.2-172.16.11.254    |
| 92    | 172.16.20.0/23    | 172.16.20.1    | 172.16.20.2-172.16.21.254    |
| 93    | 172.16.30.0/23    | 172.16.30.1    | 172.16.30.2-172.16.31.254    |


---
# Verification
