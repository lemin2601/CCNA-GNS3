##Hướng dẫn cấu hình RIP v2 mạng bên dưới

![Settings Window](https://raw.githubusercontent.com/lemin2601/CCNA-GNS3/master/RIP-v2/screenshot.png)

##==========R1==================
```
conf t
interface loopback 1
ip address 172.16.0.1 255.255.224.0
no shut
interf s0/0
ip add 172.16.60.1 255.255.255.252
no shut
interf s0/1
ip add 172.16.60.13 255.255.255.252
no shut
exit
router rip
version 2
network 172.16.0.0
network 172.16.60.0
network 172.16.60.12
no auto-summary
exit
exit
wr
```
===========R2====================
```conf t
interface loopback 2
ip add 172.16.32.1 255.255.240.0
no shut
interf s0/0
ip add 172.16.60.2 255.255.255.252
no shut
interf s0/1
ip add 172.16.60.5 255.255.255.252
no shut
exit
router rip
version 2
network 172.16.32.0
network 172.16.60.0
network 172.16.60.4
no auto-summary
exit
exit
wr
```
##===========R3====================
```conf t
interface loopback 3
ip add 172.16.48.1 255.255.248.0
no shut
interf s0/0
ip add 172.16.60.9 255.255.255.252
no shut
interf s0/1
ip add 172.16.60.6 255.255.255.252
no shut
exit
router rip
version 2
network 172.16.48.0
network 172.16.60.4
network 172.16.60.8
no auto-summary
exit
exit
wr
```
##===========R4====================
```conf t
interface loopback 4
ip add 172.16.56.1 255.255.252.0
no shut
interf s0/0
ip add 172.16.60.10 255.255.255.252
no shut
interf s0/1
ip add 172.16.60.14 255.255.255.252
no shut
interf f1/0
ip add 6.9.6.2 255.255.255.0
no shut
exit
ip route 0.0.0.0 0.0.0.0 f1/0
router rip
version 2
redistribute static
network 172.16.56.0
network 172.16.60.12
network 172.16.60.8
no auto-summary
exit
exit
wr
```
##===========IPS===============
```conf t
interf f1/0
ip add 6.9.6.1 255.255.255.0
no shut
exit
ip route 0.0.0.0 0.0.0.0 f1/0
exit
wr
```
##===========================
1```72.16.0.0/16
hosts 6996hosts <(8192 = 2^13)
172.16.0.0 -- 172.16.(8192/256-1).255/ 16-(32-16-13) = 19 

```
