# Enterprise-Campus-Network-Hotel
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
* **Inter-VLAN Departmental Isolation & Micro-segmentation:** Extended ACLs prevent non-privileged operational departments (e.g., Reception, Store, Sales, and Logistics) from accessing restricted administrative and accounting databases (Finance and Executive Admin), while strictly enforcing bidirectional mutual isolation between the Finance and Executive Admin zones to prevent unauthorized lateral movement between sensitive departments.
* **Port Security on Critical Access Ports:** The IT Department switch strictly enforces MAC-based Port Security on port `Fa0/1` dedicated to the administration machine (`Test-PC`):
  * Employs sticky MAC learning.
  * Enforces a violation mode of `shutdown` upon encountering unauthorized hardware.


* **Secure Remote Management (SSHv2):**
  * Telnet is permanently disabled on all routing appliances in favor of SSHv2.
  * Management Plane ACLs bound to `line vty` restrict remote administrative login privileges exclusively to the `Test-PC` / IT management subnet.

---
# Cisco Packet Tracer
สามารถโหลดไฟล์ Cisco Packet Tracer (.pkt) ได้ที่ [Click here to download](https://drive.google.com/file/d/1M30hpUoMjS6MDLg78o6NvnV5mzscutTJ/view?usp=sharing)

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
# Verification
| **หมวดหมู่**    | **ข้อกำหนดทางเทคนิค (Requirement)**  | **คำสั่งตรวจสอบ / วิธีทดสอบ (Verification Command)** | **ผลลัพธ์ที่คาดหวัง (Expected Outcome)** | **หลักฐานภาพถ่าย (Evidence / Screenshot)** |
| - | - | - | - | - |
| Physical Topology | Full-Mesh Serial Core Backbone | `show ip interface brief`,`show ip route` | พอร์ต Serial บน R1, R2, R3 ขึ้น up/up และรู้จักเส้นทางวง Serial Core Backbone | <img width="1766" height="996" alt="image" src="https://github.com/user-attachments/assets/3bade50d-83e1-4ec6-9692-4d740ef51d84" /> <img width="808" height="795" alt="image" src="https://github.com/user-attachments/assets/e5077ed4-957e-45a5-946b-51fea2b4e17d" /> <img width="830" height="828" alt="image" src="https://github.com/user-attachments/assets/64d61120-3c85-4eb0-acc2-f067f3aad90c" /> <img width="797" height="785" alt="image" src="https://github.com/user-attachments/assets/4066319a-9636-4803-82ae-33621d4745cb" /> |
| Physical Topology | Dual Edge Transit Infrastructure | ตรวจสอบการเชื่อมต่อทางกายภาพ R2/R3 และ Dual ISP เข้า Edge Switches | ทราฟฟิก WAN Edge เชื่อมต่อไปยังทั้ง 2 สวิตช์อย่างสมบูรณ์ | <img width="572" height="395" alt="image" src="https://github.com/user-attachments/assets/b2f65333-da43-476b-865b-f8db2f1d1ca7" /> |
| Layer 2 Resiliency | LACP EtherChannel Trunk (802.3ad) | `show etherchannel summary` บน Edge-SW1 และ SW2 | Group 1 ขึ้นแฟลก `SU` โปรโตคอล `LACP` และพอร์ตสมาชิกติดแฟลก `(P)` | <img width="574" height="358" alt="image" src="https://github.com/user-attachments/assets/e6c7db38-0ccc-4a07-a321-44f238d19034" /> <img width="577" height="359" alt="image" src="https://github.com/user-attachments/assets/57f6fb6f-145d-4d85-b09d-f03f1cf0a7fa" /> |
| Layer 2 Resiliency | Router-on-a-Stick (802.1Q) & Native VLAN 999 | `show interfaces trunk` บน Switch ทุกชั้น | พอร์ต Trunk ขึ้น `trunking`, Native VLAN เป็น 999 และ Prune เฉพาะ VLAN ประจำชั้น | <img width="664" height="237" alt="image" src="https://github.com/user-attachments/assets/e5bf9ac3-08b7-4e1e-9859-bcee6ec85e8e" /> <img width="663" height="240" alt="image" src="https://github.com/user-attachments/assets/c65230b3-8358-4d76-b321-a2388dc626f5" /> <img width="667" height="244" alt="image" src="https://github.com/user-attachments/assets/0107eb7f-9004-44b3-9943-801e211b1747" />|
| Layer 2 Resiliency | STP PortFast & BPDU Guard | `show spanning-tree summary` | `Access Ports` มีสถานะ `PortFast enabled` และ `BPDU Guard enabled` | <img width="327" height="228" alt="image" src="https://github.com/user-attachments/assets/80aaca23-0c6a-465b-8459-657f75aae7c5" />|
| Layer 2 Resiliency | Quarantine Unused Ports to Blackhole VLAN | `show interfaces status`,`show vlan brief` | พอร์ตที่ไม่ได้ใช้งานถูกสั่ง `shutdown` และย้ายไปสังกัด `VLAN 999` | <img width="851" height="566" alt="image" src="https://github.com/user-attachments/assets/a12d6469-1308-4961-936f-ab82d53c159e" />|
| IP & Services | RFC 1918 Hierarchy & Subnet Allocation |  |  | (https://github.com/Kant1593/Enterprise-Campus-Network-Hotel/edit/main/README.md#ip-addressing-table) |
| IP & Services | Multi-Pool DHCP Server & IP Exclusion | `show ip dhcp binding`,`show ip dhcp pool` บน R1, R2, R3 | แจก IP ถูกต้องตาม Subnet, เริ่มต้นแจกที่ .11 (กัน .1 - .10) | <img width="753" height="220" alt="image" src="https://github.com/user-attachments/assets/5227386d-0fed-4c52-8662-a7a4b4787c82" /> <img width="811" height="739" alt="image" src="https://github.com/user-attachments/assets/36e666ff-bdfd-4564-a65f-aa000f13beb6" /> <img width="502" height="66" alt="image" src="https://github.com/user-attachments/assets/40f34e82-d21c-4f5e-b8ac-9bd1b2406de7" />|
| IP & Services | Dedicated Departmental Network Printers | ตรวจสอบสถานะและ IP Configuration ของ Printer | เครื่องพิมพ์ประจำแผนกมี Static IP อยู่ในช่วง .2 และตอบสนอง Ping | <img width="565" height="268" alt="image" src="https://github.com/user-attachments/assets/21ca3cae-6dbf-484a-8a5c-ef7355dc4764" /><img width="1245" height="302" alt="image" src="https://github.com/user-attachments/assets/a0a1376e-05a8-4096-b96d-5d58e05d6c5f" /> จาก PC ในแผนก Finance ping printer ในแผนก |
| Dynamic Routing | OSPF Backbone | `show ip ospf neighbor` บน R1, R2, R3 | เราเตอร์ทุกตัวสถาปนาสถานะ OSPF Neighbor ผ่าน Serial เป็น `FULL/ -` |  <img width="781" height="121" alt="image" src="https://github.com/user-attachments/assets/660b0a9d-abf2-4def-99e5-1c98cd12b0dc" /> <img width="775" height="119" alt="image" src="https://github.com/user-attachments/assets/c65686e0-c322-413e-972a-5e4ecfbee3e3" /> <img width="785" height="121" alt="image" src="https://github.com/user-attachments/assets/d1cb1107-dca7-4daa-9dad-d4c85942e65f" /> |
| Dynamic Routing | OSPF Passive Interfaces | `show ip ospf interface` บน R1, R2, R3 | ขา Sub-interface ของ LAN และ Wi-Fi แสดงข้อความ `No Hellos (Passive)` | <img width="713" height="226" alt="image" src="https://github.com/user-attachments/assets/53c50f2c-da82-4917-bc19-0766397c3bb2" /> อันนี้คือแบบ No Hellos (Passive) ซึ่งไม่มีการส่ง OSPF HELLO ออกจาก Interface นี้ <img width="771" height="156" alt="image" src="https://github.com/user-attachments/assets/dab071ea-e1c7-434d-b3d5-e69211b30e93" /> มีการแลก LSA เพื่อสร้าง LSDB |
| Dynamic Routing | Default Route Injection & Metric Tuning | `show ip route` บน R1 และ R2 | R1 รับ Default Route ออก R3 ทางเดียว; R2 มีเส้นทาง OSPF ชนะ Static AD 120 | <img width="1642" height="438" alt="image" src="https://github.com/user-attachments/assets/afc708d9-8305-4ebf-8c46-d34853ce0948" />|
| WAN Edge | HSRP Gateway Redundancy | show standby brief บน R2 และ R3 | R3 เป็น `Active` (Priority 110, Preempt on) และ R2 เป็น `Standby` (Priority 100) | <img width="1635" height="108" alt="image" src="https://github.com/user-attachments/assets/6f92bad3-83af-42a0-9e97-e229464802e2" />|
| WAN Edge | Multi-Homed Floating Static Route Failover | show ip route เมื่อจำลองปิด ISP หลัก | R2 สลับการส่งต่อทราฟฟิกไปหา Backup ISP (AD 130) เมื่อเส้นทางหลักไม่พร้อมใช้งาน |  |
| WAN Edge | Dynamic PAT (NAT Overload) | show ip nat translations,show ip nat statistics | แพ็กเก็ตภายในถูกแปลง Private IP เป็น Public IP ขานอกขณะออกอินเทอร์เน็ต | <img width="755" height="260" alt="image" src="https://github.com/user-attachments/assets/5fbd0878-df4d-413f-8215-56f0b622ddf4" /> <img width="481" height="63" alt="image" src="https://github.com/user-attachments/assets/0567536e-d4ea-4a33-b74d-67b786ca3129" /> ที่ R2 ไม่แสดง เพราะ R3 เป็น HSRP Gateway Active Router จึงต้อง route ไปให้ R3 เพื่อส่งออกนอก Internet|
| Security & Hardening | Zero-Trust Wi-Fi Extended ACL | Ping จาก Wi-Fi ไป Gateway, Internet และ LAN | Ping เกตเวย์และอินเทอร์เน็ตผ่าน แต่ถูกปฏิเสธเมื่อพยายามเข้าวงภายใน | <img width="588" height="474" alt="image" src="https://github.com/user-attachments/assets/f4e32a29-7b92-476c-8c81-c538b3002737" /> อุปกรณ์ที่เชื่อมกับ WIFI ไม่สามารถ Ping วงในทั้งหมดได้เลย <img width="569" height="265" alt="image" src="https://github.com/user-attachments/assets/22eb346e-9f73-447e-b026-110e3256a8ce" /> แต่สามารถต่อออกเน็ตได้แค่นั้น |
| Security & Hardening | Inter-VLAN Isolation | Ping จากแผนกทั่วไป | แผนกทั่วไปเชื่อมต่อกันเองได้ แต่ขึ้น Destination Host Unreachable เมื่อไปยัง Finance/Admin และไม่สามารถสื่อสารข้ามกันระหว่าง Finance กับ Admin ได้ (Mutual Isolation / Dropped ทั้งสองทิศทาง) | <img width="371" height="96" alt="image" src="https://github.com/user-attachments/assets/88d5b4cb-cfb2-4d02-853b-c1f38c24006e" /> |
| Security & Hardening | Port Security on IT Admin Machine | เอา PC เครื่องอื่นมาเสียบแทน Test-PC | port fa0/1 ต้องปิดการทำงานจาก Violation Mode Shutdown MAC แบบ Sticky | Before <br> <img width="295" height="418" alt="image" src="https://github.com/user-attachments/assets/32c23385-7190-4729-99f9-06c22e75ceab" /> <br> After <br> <img width="352" height="444" alt="image" src="https://github.com/user-attachments/assets/71d67807-88f9-4048-a894-6625bd4c7ded" /> <br> port f0/1 ที่ต่อกับ Test-PC ก่อนหน้านี้ปิดการทำงาน เนื่องจากมีอุปกรณ์อื่นที่เลข MAC Address ไม่ตรงกันกับ Test-PC |
| Security & Hardening | Secure Remote Management | ทดสอบ SSH และ Telnet เข้า R1, R2, R3 | Telnet ถูกปฏิเสธ; SSHv2 เข้าได้เฉพาะจาก Subnet ของ IT เท่านั้น | <img width="301" height="164" alt="image" src="https://github.com/user-attachments/assets/c6a6d6ef-0429-4a69-9ef0-705a047ed2cd" /> <br> สามารถ SSH จาก PC Subnet ของ IT ได้ <br> <img width="506" height="90" alt="image" src="https://github.com/user-attachments/assets/20dc2b61-9ec0-47be-ba65-165b34a2bfda" /> โดนปฏิเศษการเข้าถึงเพราะใช้ Telnet <img width="371" height="64" alt="image" src="https://github.com/user-attachments/assets/989b8a73-b436-4f1f-939d-cf4c54ae5be7" /> <br> ถ้าไม่ได้มี IP Subnet ของ IT จะโดนปฏิเศษการเข้าถึง |
