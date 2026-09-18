# Enterprise-Campus-Network-Hotel
---

# Verification
| **หมวดหมู่**    | **ข้อกำหนดทางเทคนิค (Requirement)**  | **คำสั่งตรวจสอบ / วิธีทดสอบ (Verification Command)** | **ผลลัพธ์ที่คาดหวัง (Expected Outcome)** | **หลักฐานภาพถ่าย (Evidence / Screenshot)** |
| - | - | - | - | - |
| Physical Topology | Full-Mesh Serial Core Backbone | `show ip interface brief`,`show ip route` | พอร์ต Serial บน R1, R2, R3 ขึ้น up/up และรู้จักเส้นทางวง Serial Core Backbone | <img width="1766" height="996" alt="image" src="https://github.com/user-attachments/assets/3bade50d-83e1-4ec6-9692-4d740ef51d84" /> <img width="808" height="795" alt="image" src="https://github.com/user-attachments/assets/e5077ed4-957e-45a5-946b-51fea2b4e17d" /> <img width="830" height="828" alt="image" src="https://github.com/user-attachments/assets/64d61120-3c85-4eb0-acc2-f067f3aad90c" /> <img width="797" height="785" alt="image" src="https://github.com/user-attachments/assets/4066319a-9636-4803-82ae-33621d4745cb" /> |
| Physical Topology | Dual Edge Transit Infrastructure | ตรวจสอบการเชื่อมต่อทางกายภาพ R2/R3 และ Dual ISP เข้า Edge Switches | ทราฟฟิก WAN Edge เชื่อมต่อไปยังทั้ง 2 สวิตช์อย่างสมบูรณ์ | <img width="572" height="395" alt="image" src="https://github.com/user-attachments/assets/b2f65333-da43-476b-865b-f8db2f1d1ca7" /> |
| Layer 2 Resiliency | LACP EtherChannel Trunk (802.3ad) | `show etherchannel summary` บน Edge-SW1 และ SW2 | Group 1 ขึ้นแฟลก `SU` โปรโตคอล `LACP` และพอร์ตสมาชิกติดแฟลก `(P)` | <img width="574" height="358" alt="image" src="https://github.com/user-attachments/assets/e6c7db38-0ccc-4a07-a321-44f238d19034" /> <img width="577" height="359" alt="image" src="https://github.com/user-attachments/assets/57f6fb6f-145d-4d85-b09d-f03f1cf0a7fa" /> |
| Layer 2 Resiliency | Router-on-a-Stick (802.1Q) & Native VLAN 999 | `show interfaces trunk` บน Switch ทุกชั้น | พอร์ต Trunk ขึ้น `trunking`, Native VLAN เป็น 999 และ Prune เฉพาะ VLAN ประจำชั้น |  |
| Layer 2 Resiliency | STP PortFast & BPDU Guard | `show spanning-tree summary` | `Access Ports` มีสถานะ `PortFast enabled` และ `BPDU Guard enabled` |  |
| Layer 2 Resiliency | Quarantine Unused Ports to Blackhole VLAN | `show interfaces status`,`show vlan brief` | พอร์ตที่ไม่ได้ใช้งานถูกสั่ง `shutdown` และย้ายไปสังกัด `VLAN 999` |  |
| IP & Services | RFC 1918 Hierarchy & Subnet Allocation |  |  | (https://github.com/Kant1593/Enterprise-Campus-Network-Hotel/edit/main/README.md#ip-addressing-table) |
| IP & Services | Multi-Pool DHCP Server & IP Exclusion | `show ip dhcp binding`,`show ip dhcp pool` บน R1, R2, R3 | แจก IP ถูกต้องตาม Subnet, เริ่มต้นแจกที่ .11 (กัน .1 - .10) |  |
| IP & Services | Dedicated Departmental Network Printers | ตรวจสอบสถานะและ IP Configuration ของ Printer | เครื่องพิมพ์ประจำแผนกมี Static IP อยู่ในช่วง .2 และตอบสนอง Ping |  |
| Dynamic Routing | OSPF Backbone | `show ip ospf neighbor` บน R1, R2, R3 | เราเตอร์ทุกตัวสถาปนาสถานะ OSPF Neighbor ผ่าน Serial เป็น `FULL/ -` |  |
| Dynamic Routing | OSPF Passive Interfaces | `show ip ospf interface` บน R1, R2, R3 | ขา Sub-interface ของ LAN และ Wi-Fi แสดงข้อความ `No Hellos (Passive)` |  |
| Dynamic Routing | Default Route Injection & Metric Tuning | `show ip route` บน R1 และ R2 | R1 รับ Default Route ออก R3 ทางเดียว; R2 มีเส้นทาง OSPF ชนะ Static AD 120 |  |
| WAN Edge | HSRP Gateway Redundancy | show standby brief บน R2 และ R3 | R3 เป็น `Active` (Priority 110, Preempt on) และ R2 เป็น `Standby` (Priority 100) |  |
| WAN Edge | Multi-Homed Floating Static Route Failover | show ip route เมื่อจำลองปิด ISP หลัก | R2 สลับการส่งต่อทราฟฟิกไปหา Backup ISP (AD 130) เมื่อเส้นทางหลักไม่พร้อมใช้งาน |  |
| WAN Edge | Dynamic PAT (NAT Overload) | show ip nat translations,show ip nat statistics | แพ็กเก็ตภายในถูกแปลง Private IP เป็น Public IP ขานอกขณะออกอินเทอร์เน็ต |  |
| WAN Edge | ISP Reverse Route Target to HSRP VIP | show ip route บน ISP Router | มีเส้นทาง Static Aggregate วง `192.168.0.0/16` และ `172.16.0.0/16` ชี้มาที่ VIP |  |
| Security & Hardening | Zero-Trust Wi-Fi Extended ACL | Ping จาก Wi-Fi ไป Gateway, Internet และ LAN | Ping เกตเวย์และอินเทอร์เน็ตผ่าน แต่ถูกปฏิเสธเมื่อพยายามเข้าวงภายใน |  |
| Security & Hardening | Inter-VLAN Isolation | Ping จากแผนกทั่วไป | เข้าถึงแผนกทั่วไปได้ แต่ขึ้น Destination Host Unreachable ไปยัง Finance/Admin |  |
| Security & Hardening | Port Security on IT Admin Machine | เอา PC เครื่องอื่นมาเสียบแทน Test-PC | port fa0/1 ต้องปิดการทำงานจาก Violation Mode Shutdown MAC แบบ Sticky |  |
| Security & Hardening | Secure Remote Management | ทดสอบ SSH และ Telnet เข้า R1, R2, R3 | Telnet ถูกปฏิเสธ; SSHv2 เข้าได้เฉพาะจาก Subnet ของ IT เท่านั้น |  |
---
### Project Objectives

The primary objective of the **Vic Hotel Network Infrastructure Project** is to design, implement, and validate a highly available, secure, and scalable enterprise campus network for a three-story commercial facility. The technical goals include:

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
  * Telnet is permanently disabled on all routing appliances in favor of SSHv2.
  * Management Plane ACLs bound to `line vty` restrict remote administrative login privileges exclusively to the `Test-PC` / IT management subnet.

---
# Cisco Packet Tracer
สามารถโหลดไฟล์ Cisco Packet Tracer (.pkt) ได้ที่ [Click here to download](https://drive.google.com/file/d/1CZ73U3c0N1ihZB7oG54o21SNfZ3rJTAp/view?usp=sharing)

รูปภาพ topology ใน Cisco Packet Tracer
<img width="2739" height="2125" alt="logical network diagram" src="https://github.com/user-attachments/assets/5083f518-8d90-4127-a1a8-0aa7ffc03cd3" />

---
# Topology
สามารถเข้ามาดู Topology ได้ที่ [Click Here View](https://drive.google.com/file/d/1CtQH33iUcUt0y91gwiHrdgJ_3EvV5zOz/view?usp=sharing)

รูปภาพ logical network topology จาก link ข้างต้น
<img width="5848" height="5288" alt="image" src="https://github.com/user-attachments/assets/f027497f-c134-4d9b-8044-1bc7a6bc31a2" />

---
# IP Addressing Table
| VLAN ID    | Network Address |  Default Gateway |  Usable Range |
| :---: | :---: | :---: | :---: |
| 10    | 192.168.1.0/24    | 192.168.1.1    | 192.168.1.11-192.168.1.254    |
| 20    | 192.168.2.0/24    | 192.168.2.1    | 192.168.2.11-192.168.2.254    |
| 30    | 192.168.3.0/24    | 192.168.3.1    | 192.168.3.11-192.168.3.254    |
| 40    | 192.168.4.0/24    | 192.168.4.1    | 192.168.4.11-192.168.4.254    |
| 50    | 192.168.5.0/24    | 192.168.5.1    | 192.168.5.11-192.168.5.254    |
| 60    | 192.168.6.0/24    | 192.168.6.1    | 192.168.6.11-192.168.6.254    |
| 70    | 192.168.7.0/24    | 192.168.7.1    | 192.168.7.11-192.168.7.254    |
| 80    | 192.168.8.0/24    | 192.168.8.1    | 192.168.8.11-192.168.8.254    |
| 91    | 172.16.10.0/23    | 172.16.10.1    | 172.16.10.11-172.16.11.254    |
| 92    | 172.16.20.0/23    | 172.16.20.1    | 172.16.20.11-172.16.21.254    |
| 93    | 172.16.30.0/23    | 172.16.30.1    | 172.16.30.11-172.16.31.254    |


---

