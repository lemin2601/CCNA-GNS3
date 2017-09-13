# **Hướng dẫn cấu hình Access-list mạng bên dưới**

![Settings Window](https://raw.githubusercontent.com/lemin2601/CCNA-GNS3/master/Access-list/screenshot.png)

## Assignment 1
- Deny IP connections between LAN 1 and LAN 4
- Deny all connections from LAN 1 to the Internet, except Web access
- Other traffics are allowed
## Assignment 2
- Deny all connections between LAN 2 and LAN 4, except FTP from LAN 2 to LAN 4 (one way)
- Deny all connections between LAN 2 and LAN 3, except HTTP between them (two way)
- Other traffics are allowed
## Assignment 3
- Hosts from Internet can not ping the inside hosts
- Hosts from Internet can not initiate the TCP connections to the inside hosts, except Web access to
the servers: 10.10.1.2/24 and 10.10.2.2/24
- Other traffics are allowed
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
!Router WEST
conf t
no ip access-list ext assignment
ip access-list ext assignment
remark Assignment 1
remark Deny IP connections between LAN 1 and LAN 4
deny ip 10.10.1.0 0.0.0.255 10.10.4.0 0.0.0.255 log
remark Deny all connections from LAN 1 to the Internet, except Web access
permit tcp 10.10.1.0 0.0.0.255 any eq www log
remark Cho Phep Truy cap http tu interServer
permit tcp 10.10.1.0 0.0.0.255 eq www any established log
deny ip 10.10.1.0 0.0.0.255 any log
remark Assignment 2
remark Deny all connections between LAN 2 and LAN 4, except FTP from LAN 2 to LAN 4 (one way)
permit tcp 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255 eq ftp log
permit tcp 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255 eq ftp-data log
permit tcp 10.10.2.0 0.0.0.255 gt 1023 10.10.4.0 0.0.0.255  log
deny ip 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255
remark Deny all connections between LAN 2 and LAN 3, except HTTP between them (two way)
permit tcp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 eq www log
permit tcp 10.10.2.0 0.0.0.255 eq www 10.10.3.0 0.0.0.255 established log
deny ip 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 log
permit ip any any log
exit
inter s0/0
ip access-group assignment out
exit
exit
wr

!Router East
conf t
no ip access-list ext assignment
ip access-list ext assignment
remark Assigment 2
remark Deny all connections between LAN 2 and LAN 4, except FTP from LAN 2 to LAN 4 (one way)
permit tcp 10.10.4.0 0.0.0.255 10.10.2.0 0.0.0.255 eq www established log
permit tcp 10.10.4.0 0.0.0.255 10.10.2.0 0.0.0.255 gt 1023 established log
remark Cho Phep Truy cap http tu interServer
permit tcp 10.10.1.0 0.0.0.255 eq www any established log
deny ip 10.10.4.0 0.0.0.255 10.10.2.0 0.0.0.255 log
remark Deny all connections between LAN 2 and LAN 3, except HTTP between them (two way)
permit tcp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 eq www log
permit tcp 10.10.3.0 0.0.0.255 eq www 10.10.2.0 0.0.0.255 established log
deny ip 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 log
permit ip any any log
exit
inter s0/1
ip access-group assignment out
exit
exit
wr

!Router Gateway
conf t
no ip access-list ext assignment
ip access-list ext assignment
remark Assignment 3
deny icmp any any echo log
!permit tcp any 10.10.1.2 0.0.3.0 eq 80 log
permit tcp any 10.10.1.2 0.0.0.0 eq 80 log
permit tcp any 10.10.4.2 0.0.0.0 eq 80 log
permit tcp any any established log
deny tcp any any log
permit ip any any log
exit
inter f1/0
ip access-group assignment in
exit
exit
wr



```
