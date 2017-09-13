# **Hướng dẫn cấu hình Access-list mạng bên dưới**

![Settings Window](https://raw.githubusercontent.com/lemin2601/CCNA-GNS3/master/Tuan4-Access-list-4-5-6/screenshot.png)

## Assignment 4
!Refer to the above network diagram, after finishing the routing between subnets (you can use any routing
!protocol), implementing the Access Control List (ACL) to satisfy the following conditions:
!- Internet cannot ping into inner network
!- Don’t allow TCP traffic from outside going in, but going out is allowable
!- Other traffics are allowed 

## Assignment 5
!Refer to the above network diagram, after finishing the routing between subnets (you can use any routing
!protocol), implementing the Access Control List (ACL) to satisfy the following conditions:
!- Deny all traffic from LAN1 to outside network, except WEB access
!- Don’t allow IP traffic between LAN1 & LAN2, LAN1 & LAN3
!- Other traffics are allowed

## Assignment 6
!Refer to the above network diagram, after finishing the routing between subnets (you can use any routing
!protocol), implementing the Access Control List (ACL) to satisfy the following conditions:
!- Allow FTP traffic between LAN2 and LAN3
!- Don’t allow IP traffic between LAN2 and LAN3
!- Other traffics are allowed 

# Routing 
## ===============WEST===================
```
conf t
inter f1/0
ip add 10.10.1.1 255.255.255.0
no shut
inter f2/0
ip add 10.10.2.1 255.255.255.0
no shut
inter s0/0
ip add 172.16.3.1 255.255.255.0
no shut
exit
router rip
version 2
network 10.10.1.0
network 10.10.2.0
network 172.16.3.1
no auto
exit
exit
wr
```
## ============= East ===================
```
conf t
inter f1/0
ip add 10.10.3.1 255.255.255.0
no shut
inter f2/0
ip add 10.10.4.1 255.255.255.0
no shut
inter s0/1
ip add 172.16.4.1 255.255.255.0
no shut
exit
router rip
version 2
network 10.10.3.0
network 10.10.4.0
network 172.16.4.1
no auto
exit
exit
wr
```
## ====================Geateway =============
```
conf t
interf f1/0
ip add 16.19.16.19 255.255.255.0
no shut
inter s0/0
ip add 172.16.3.2 255.255.255.0
no shut
inter s0/1
ip add 172.16.4.2 255.255.255.0
no shut
exit
ip route 0.0.0.0 0.0.0.0 f1/0
router rip
version 2
redistribute static
network 172.16.3.0
network 172.16.4.0
no auto-summary
exit
exit
wr
```
## ======INter net=================
```
conf t
interf f0/0
ip add 1.1.1.1 255.255.255.0
no sh
interf f1/0
ip add 16.19.16.20 255.255.255.0
no shut
exit
ip route 0.0.0.0 0.0.0.0 f1/0
exit
wr

```
# Access-list
## ======Gate Way =====
```

conf t !ROUTER Gateway
no ip access-list ext AssignmentIn
no ip access-list ext AssignmentOut
ip access-list ext AssignmentIn
remark - Internet cannot ping into inner network
deny icmp any any echo log
permit icmp any any log 
remark - Don’t allow TCP traffic from outside going in, but going out is allowable
permit tcp any any established log
deny tcp any any log 
remark - Other traffics are allowed 
permit ip any any log 
exit
ip access-list ext AssignmentOut
remark - Deny all traffic from LAN1 to outside network, except WEB access
permit tcp 10.10.1.0 0.0.0.255 any eq www log 
deny ip 10.10.1.0 0.0.0.255 any log 
remark - Other traffics are allowed 
permit ip any any log 
exit
interface f1/0
ip access-group AssignmentIn in
ip access-group AssignmentOut out
end
wr
```
## ========router west=============
```

conf t !router west
no ip access-list ext AssignmentInLan1
no ip access-list ext AssignmentInLan2
ip access-list ext AssignmentInLan1
remark - Don’t allow IP traffic between LAN1 & LAN2, LAN1 & LAN3
deny ip 10.10.1.0 0.0.0.255 10.10.2.0 0.0.0.255 log 
deny ip 10.10.1.0 0.0.0.255 10.10.3.0 0.0.0.255 log 
remark - Other traffics are allowed 
permit ip any any log 
exit
ip access-list ext AssignmentInLan2
remark - Don’t allow IP traffic between LAN1 & LAN2, LAN1 & LAN3
deny ip 10.10.2.0 0.0.0.255 10.10.1.0 0.0.0.255  log 
remark - Allow FTP traffic between LAN2 and LAN3 
permit tcp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 eq ftp log 
permit tcp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 eq ftp-data log 
permit tcp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 gt 1023 log 
remark - Don’t allow IP traffic between LAN2 and LAN3
deny ip 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 log 
remark - Other traffics are allowed 
permit ip any any log 
interface  f1/0
ip access-group AssignmentInLan1 in
interface f2/0
ip access-group AssignmentInLan2 in
end
wr
```
## ========= router East=================
```

conf t ! router EAST
no ip access-list ext AssignmentInLan3
ip access-list ext AssignmentInLan3
remark - Don’t allow IP traffic between LAN1 & LAN2, LAN1 & LAN3
deny ip 10.10.3.0 0.0.0.255 10.10.1.0 0.0.0.255 log
remark - Allow FTP traffic between LAN2 and LAN3
permit tcp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 eq ftp log 
permit tcp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 eq ftp-data log 
permit tcp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 gt 1023 log
remark - Don’t allow IP traffic between LAN2 and LAN3
deny ip 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 log 
remark - Other traffics are allowed 
permit ip any any log 
exit
interface f1/0
ip access-group AssignmentInLan3 in
end
wr

```
