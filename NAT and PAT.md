## Network Address Translation (NAT) and Port Address Translation (PAT)
<img width="754" alt="NAT and PAT" src="https://github.com/arundhik97/CCNP/assets/38269066/70647a04-e068-411a-9e01-27c7a56907e2">

Initial Configurations:<br>
R1<br>
R1(config-if)#int f1/0<br>
R1(config-if)#ip add 203.0.1.2 255.255.255.0<br>
R1(config-if)#no shut<br>

R2<br>
R2#conf t<br>
Enter configuration commands, one per line.  End with CNTL/Z.<br>
R2(config)#int f0/0<br>
R2(config-if)#ip add 192.168.1.1 255.255.255.0<br>
R2(config-if)#no shut<br>
R2(config-if)#

R2(config-if)#int f1/0<br>
R2(config-if)#ip add 203.0.1.1 255.255.255.0<br>
R2(config-if)#no shut<br>

Default route from R1 f1/0 to R2 F1/0 <br>
R1(config-if)#int f1/0<br>
R1(config-if)#ip route 0.0.0.0 0.0.0.0 203.0.1.1<br>
R1(config)#<br>

NAT inside and outside <br>
R2(config-if)#int f0/0<br>
R2(config-if)#ip nat in<br>
R2(config-if)#ip nat inside<br>
R2(config-if)#int<br>
*Mar  1 00:10:42.071: %LINEPROTO-5-UPDOWN: Line protocol on Interface NVI0, changed state to up<br>
R2(config-if)#int f1/0<br>
R2(config-if)#ip nat outside<br>

R2(config-if)#ip access-list standard ArunNAT<br>
R2(config-std-nacl)#pe<br>
R2(config-std-nacl)#permit 192.168.1.0 0.0.0.255<br>
R2(config-std-nacl)#ip nat pool ArunPOOL 172.16.1.0 172.16.1.100 ne<br>
R2(config-std-nacl)#ip nat pool ArunPOOL 172.16.1.0 172.16.1.100 net<br>
R2(config-std-nacl)#$ ArunPOOL 172.16.1.0 172.16.1.100 netmask 255.255.255.0<br>
R2(config)#ip nat in<br>
R2(config)#ip nat inside sou<br>
R2(config)#ip nat inside source li<br>
R2(config)#ip nat inside source list ArunNAT pool ArunPOOL<br>
R2(config)#exit<br>
R2#<br>

Pinging from PC2 to R1<br>
PC2> ping 203.0.1.2<br>
203.0.1.2 icmp_seq=1 timeout<br>
84 bytes from 203.0.1.2 icmp_seq=2 ttl=254 time=45.500 ms<br>
84 bytes from 203.0.1.2 icmp_seq=3 ttl=254 time=45.189 ms<br>
84 bytes from 203.0.1.2 icmp_seq=4 ttl=254 time=45.601 ms<br>
84 bytes from 203.0.1.2 icmp_seq=5 ttl=254 time=45.477 ms<br>

Show IP NAT Translations:<br>
R2#sh ip nat translations<br>
Pro Inside global      Inside local       Outside local             Outside global<br>
icmp 172.16.1.1:14686  192.168.1.101:14686 203.0.1.2:14686   203.0.1.2:14686<br>
icmp 172.16.1.1:15198  192.168.1.101:15198 203.0.1.2:15198   203.0.1.2:15198<br>
icmp 172.16.1.1:15454  192.168.1.101:15454 203.0.1.2:15454   203.0.1.2:15454<br>
icmp 172.16.1.1:15710  192.168.1.101:15710 203.0.1.2:15710   203.0.1.2:15710<br>
icmp 172.16.1.1:15966  192.168.1.101:15966 203.0.1.2:15966   203.0.1.2:15966<br>
--- 172.16.1.1         192.168.1.101      ---                ---<br>

Lets suppose there in only one Public IP to route to internet. That’s where Port Address Translation becomes handy. For that, <br>
R1(config)#ip nat inside source list 1 interface f0/1 overload<br>
This line translates all nat inside IP to public IP attaching port number to each IP translations.<br>
<img width="455" alt="PAT trans" src="https://github.com/arundhik97/CCNP/assets/38269066/31586e1e-7c44-4cc0-81d1-0bfad36fd36f">
