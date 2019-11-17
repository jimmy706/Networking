# Networking
Nội dung thực hành môn Mạng máy tính
## Yêu cầu:
Danh sách các yêu cầu dưới đây là cần thiết để chạy và thiết lập các tập tin và file đính kèm:
1. Hệ điều hành Linux (Ubuntu)
2. Cài đặt Netkit [tại đây](http://wiki.netkit.org/index.php?title=Download_Official)
3. Hướng dẫn cài đặt netkit có ở trong thư mục buổi 1
## Kiểm thử:
Sau khi cài đặt thành công netkit dùng lệnh `vstart pc1` để kiểm thử đã cài đặt thành công chưa
## Vấn đề về việc thiếu folder:
Do git chỉ cập nhật các file chứ không phải folder nên các fodler rỗng như pc, client, webserver... có thể không có khi tải về. Các bạn có thể fix bằng cách tạo các folder bị thiếu tương ứng với các bài tập trong file pdf đính kèm

## Netkit available command:

### `vstart <name> --eth0=A`
Start virtual machine **name** with interface eth0 and LAN A. 

### `ifconfig eth0 <IP address> up`
Activate eth0 interface.

### `ping <IP address>`
Ping to **IP address**, if can ping to, it mean your machine is in a right network.

### `traceroute <IP address>`
Trace the path to **IP address**

### `vcrash <name>`
Crash the **name** machine

### `lstart`
Start all machines in folder

### `lcrash`
Crash all machines in folder

### `route add default gw <gateway IP>`
Add default gateway for machine (usally in pc, client machine)
Exp: `route add default gw 10.0.0.2`

### `route add -net <LAN IP> gw <gateway IP>`
Add static route to **LAN** from **gateway IP**
Exp: `route add -net 10.0.2.0/24 gw 10.0.0.2`

### `tcpdump -w /hosthome/<path>`
Catching ping data and write it to **path** file
More config:
- `-s <file size>`: maximum file size
- `-i <interface name>`: Filter interface to catch, use **any** to allow all interface go through

### `ifconfig eth0 hw ether <MAC IP>`
Config **MAC IP** for machine. 
Exp: `ifconfig eth0 hw ether 00:00:00:00:00:01`

### `brctl addbr <bridge name>`
Create new bright name **bridge name**
Exp: `brctl addbr br0`
### `brctl addif <bridge name> <interface>`
Subscribe **bridge name** to **interface**. All interface have same bridge can translate data to each other
Exp: `brctl addif br0 eth0`

### `brctl stp <bridge name> on`
Activate **stp** algorithm on **bridge name**
Exp: `brctl stp br0 on`

### `ifconfig <bridge name> up`
Activate **bridge name**

> Bridge commands usally use in **switch.startup** files. For example:
```
ifconfig eth0 up
ifconfig eth0 hw ether 00:00:00:00:00:10
ifconfig eth1 up
ifconfig eth1 hw ether 00:00:00:00:00:20
brctl addbr br0
brctl addif br0 eth0
brctl addif br0 eth1
brctl stp br0 on
ifconfig br0 up
```
---
## Autonomous System config tutorial:
An autonomous system (AS) is a collection of connected Internet Protocol (IP) routing prefixes under the control of one or more network operators on behalf of a single administrative entity or domain that presents a common, clearly defined routing policy to the internet.

Zebra is a free routing system designed for UNIX operating systems and licensed under the GNU public license. 

At this practice, we'll use zebra have already installed for example of Rip2 and Ospf algorithm.

### 1. Getting started 
To start zebra service, in **.startup** file of machine, add this line: **`/etc/init.d/zebra start`**

### 2. Config the zebra service:
At machine folder, we create **/etc/zebra/** folder and add 3 files: **zebra.conf, daemons, ripd.conf** if you use Rip algorithm or **ospfd.conf** if you use Ospf.

### 3. Config zebra.conf file:
Open **/etc/zebra/zebra.conf** file, these config is optional, you can pass this step if necessary:
```
hostname=zebra
password=zebra
enable password zebra

# Path contant log file that store activities, events
log file /var/log/zebra/zebra.log
```

### 4. Config daemons file:
Open **/etc/zebra/daemons** file and add these line if you use rip algorithm:
```
zebra=yes
ripd=yes
```
Add these line if you use ospf algorithm:
```
zebra=yes
ospfd=yes
```

### 5. Config ripd.conf (or ospfd.conf):
**ripd.conf** file:
```
router rip
network <AS area>
log file /var/log/zebra/ripd.log
```

**ospfd.conf** file:
```
# Add the cost for every interface that machine accessed
interface eth0
ospf cost 30

interface eth1
ospf cost 80

interface eth2
ospf cost 20

router ospf
network <AS network> area <AS area>
log file /var/log/zebra/ospfd.log
```
