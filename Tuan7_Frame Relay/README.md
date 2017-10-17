# **Hướng dẫn cấu hình Access-list mạng bên dưới**
# Thiết lập mạng như sơ đồ bên dưới**
![Settings Window](https://raw.githubusercontent.com/lemin2601/CCNA-GNS3/master/Tuan7_Frame Relay/screenshoot.png)

# Kết quả
"show ip route" có đầy đủ các mạng
![Settings Window](https://raw.githubusercontent.com/lemin2601/CCNA-GNS3/master/Tuan7_Frame Relay/result.PNG)
# Script
## ==========HANOI==================
```
conf t
interf s0/0
encapsulation frame-relay
no sh
exit
inter s0/0.22 point-to-point
frame interface-dlci 22
ip add 192.168.8.33 255.255.255.224
exit
interface s0/0.99 point-to-point
frame interface-dlci 99
ip add 192.168.8.130 255.255.255.224
exit
interface loopback 1
ip add 172.20.0.1 255.255.0.0
no sh
exit
router rip
version 2
network 172.20.0.0
network 192.168.8.128
network  192.168.8.32
no auto
end 
wr
```
## ==========DANANG==================
```
conf t
interf s0/0
encapsulation frame-relay
no sh
exit
inter s0/0.88 point-to-point
frame interface-dlci 88
ip add 192.168.8.129 255.255.255.224
exit
interface s0/0.77 point-to-point
frame interface-dlci 77
ip add 192.168.8.98 255.255.255.224
exit
interface loopback 1
ip add 172.21.0.1 255.255.0.0
no sh
exit
router rip
version 2
network 172.21.0.0
network 192.168.8.128
network  192.168.8.96
no auto
end
wr
```
## ==========SAI GON==================
```
conf t
interf s0/0
encapsulation frame-relay
no sh
exit
inter s0/0.66 point-to-point
frame interface-dlci 66
ip add 192.168.8.97 255.255.255.224
exit
interface s0/0.55 point-to-point
frame interface-dlci 55
ip add 192.168.8.66 255.255.255.224
exit
interface loopback 1
ip add 172.22.0.1 255.255.0.0
no sh
exit
router rip
version 2
network 172.22.0.0
network 192.168.8.64
network  192.168.8.96
no auto
end
wr

```
## ==========HUE==================
```
conf t
interf s0/0
encapsulation frame-relay
no sh
exit
inter s0/0.44 point-to-point
frame interface-dlci 44
ip add 192.168.8.65 255.255.255.224
exit
interface s0/0.33 point-to-point
frame interface-dlci 33
ip add 192.168.8.34 255.255.255.224
exit
interface loopback 1
ip add 172.23.0.1 255.255.0.0
no sh
exit
router rip
version 2
network 172.23.0.0
network 192.168.8.128
network  192.168.8.32
no auto
end
wr
```
## ==========FRAME RELAY==================
```
conf t
frame-relay switching
inter s0/0
no sh
encapsulation frame-relay
frame intf-type dce
clock rate 2000000
frame route 22 interface s0/3 33
frame route 99 interface s0/1 88
exit
inter s0/1
no sh
encapsulation frame-relay
frame intf-type dce
clock rate 2000000
frame route 88 interface s0/0 99
frame route 77 interface s0/2 66
exit
inter s0/2
no sh
encapsulation frame-relay
frame intf-type dce
clock rate 2000000
frame route 66 interface s0/1 77
frame route 55 interface s0/3 44
exit
inter s0/3
no sh
encapsulation frame-relay
frame intf-type dce
clock rate 2000000
frame route 44 interface s0/2 55
frame route 33 interface s0/0 22
end
wr

```

