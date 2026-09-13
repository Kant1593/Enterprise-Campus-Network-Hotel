# Enterprise-Campus-Network-Hotel
---

# Requirements
The hotel has three floors; in the first floor there three departments (Reception, store and Logistics), in the second floor there are three departments (Finance, HR and Sales/Marketing), while the third floor hosts the IT and Admin. Therefore, the following are part of the considerations during the design and implementation;
- There should be three routers connecting each floor (all placed in the server room in IT department).
- All routers should be connected to each other using serial DCE cable.
- The network between the routers should be 10.10.10.0/30,10.10.10.4/30 and 10.10.10.8/30.
- Each floor is expected to have one switch (placed in the respective floor).
- Each floor is expected to have WIFI networks connected to laptops and phones.
- Each department is expected to have a printer.
- Use OSPF as the routing protocol to advertise routes.
- All devices in the network are expected to obtain IP address dynamically with their respective router configured as the DHCP server.
- Configure SSH in all the routers for remote login.
- In IT department, add PC called Test-PC to port fa0/1 and use it to test remote login.
- Configure port security to IT-dept switch to allow only Test-PC to access port fa0/1 (use sticky method to obtain mac-address with violation mode of shutdown.)
---
# Cisco Packet Tracer
สามารถโหลดไฟล์ Cisco Packet Tracer (.pkt) ได้ที่ [Click here to download](https://drive.google.com/file/d/1CtQH33iUcUt0y91gwiHrdgJ_3EvV5zOz/view?usp=sharing)

รูปภาพ topology ใน Cisco Packet Tracer

---
# Topology
สามารถเข้ามาดู Topology ได้ที่ [Click Here View](https://drive.google.com/file/d/1CtQH33iUcUt0y91gwiHrdgJ_3EvV5zOz/view?usp=sharing)

รูปภาพ logical network topology จาก link ข้างต้น
<img width="5848" height="5288" alt="image" src="https://github.com/user-attachments/assets/9b12aef1-4946-431c-b5da-2edc6c194cd9" />
