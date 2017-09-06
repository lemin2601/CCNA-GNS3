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

## ======Gate Way
```
conf t
no ip access-list ext 100 !Cog s0/0 in
!no ip access-list ext 101 !cong s0/0 out
no ip access-list ext 102 ! cong s0/1 in
!no ip access-list ext 103 !cong s0/2 out
no ip access-list ext 104 ! cong f1/0 in
no ip access-list ext 105 !cong f1/0 out

inter f1/0
ip access-group 104 in
ip access-group 105 out
inter s0/0
ip access-group 100 in
inter s0/1
ip access-group 102 in
exit
ip access-list ext 100
remark "Deny Lan 1 to Lan4"
1 deny ip 10.10.1.0 0.0.0.255 10.10.4.0 0.0.0.255
remark "Deny Connect Lan2 To Lan4"
2 permit tcp 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255 eq ftp
3 permit tcp 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255 eq ftp-data
4 deny ip 10.10.2.0 0.0.0.255 10.10.4.0 0.0.0.255
remark "Deny Connect Lan2 To Lan3, Except Http"
5 permit tcp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255 eq www
6 permit icmp 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255
7 deny ip 10.10.2.0 0.0.0.255 10.10.3.0 0.0.0.255
remark "Other traffics"
8 permit ip any any
exit

ip access-list ext 102
remark "Deny Lan4 to Lan1"
1 deny ip 10.10.4.0 0.0.0.255 10.10.1.0 0.0.0.255
remark "Deny Connect Lan4 To Lan2"
2 deny ip 10.10.4.0 0.0.0.255 10.10.2.0 0.0.0.255
remark "Deny Connect Lan3 To Lan2 except http"
3 permit tcp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255 eq www
4 permit icmp 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255
5 deny ip 10.10.3.0 0.0.0.255 10.10.2.0 0.0.0.255
remark "Other traffics"
6 permit ip any any
exit

ip access-list ext 104
remark "Internet can't ping"
1 deny icmp host 16.19.16.19 any
2 permit tcp any 10.10.1.2 0.0.0.0 eq www
3 permit tcp any 10.10.2.2 0.0.0.0 eq www
4 deny ip host 16.19.16.19 any  
exit
!!!!!!!!!!!!!!!!!!!!1111
ip access-list ext 105
!Allow Lan1 to Web only
remark "Only Permit Internet Web access from Lan1"
1 permit tcp 10.10.1.0 0.0.0.255 any eq www
!2 permit icmp 10.10.1.0 0.0.0.255 any
!3 permit igmp 10.10.1.0 0.0.0.255 any
4 deny ip 10.10.1.0 0.0.0.255 any
remark "Other traffics"
5 permit ip any any
exit
exit
wr

```
