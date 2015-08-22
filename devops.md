# Check List

## System Load

### General
* uptime

```
root@ubuntu-test:~# uptime
 22:31:19 up 12 days, 20:11,  1 user,  load average: 0.00, 0.01, 0.05
- 0.00 : last 1m
- 0.01 : last 5m
- 0.05 : last 15m
```

* top

```
top -b -n 1 (-b : batch mode, -n : run once)
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
```

### Memory

RAM이 부족하여 OOM Killer 에 의해 process가 kill 된 경우 **/var/log/syslog** 에서 그 로그를 확인해 볼 수 있다.

swap은 file과 ram 사이에서 효율적인 I/O 관리를 위해 일어나는 현상이며 free 명령어를 통해 확인할 경우 그 수치 해석에 주의해야한다.

top 실행에서 M을 입력하면 memory 사용량에 따라 정렬된다.

### IO

* iostat

```
root@ubuntu-test:/var/log# iostat
Linux 3.13.0-57-generic (ubuntu-test) 	08/21/2015 	_x86_64_	(1 CPU)
avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.30    0.02    0.11    0.01    0.00   99.56
Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
vda               0.38         0.81         5.09     900897    5643476
```

iotop 이라는 툴로 대신할 수 있다

### CPU

* sysstat

/etc/default/sysstat 설정을 통해 /var/log/sa[날짜] 에 파일이 쌓임
sar 명령어를 통해 과거 이력을 볼 수 있다.

```
sar (기본 하루)
sar -r (Ram statistics)
sar -b (Disk statistics)
```

## System Boot

## Full or Corrupt Disk Issues

### Disk Full

```
df -h
du -ckx | sort -n
```

### Out of Inodes

```
df -i
```

### Repair Software RAID

```
> cat /proc/mdstat
Personalities :
unused devices: <none>
```

```
watch -n 5 "cat /proc/mdstat"
```

## Tracking Down the source of Network Problems

plugged? => interface up? => in local network? => ping gateway server => is DNS working? => can I route to the remote Host? => Remote Port open?

### Is it plugged in?
The first troubleshooting steps to perform are on the client. You first want to verify that your client’s connection to the network is healthy.

```
> ethtool eth0
Settings for eth0:
	Link detected: yes
```

### Is the Interface Up?
Once you have established that you are physically connected to the net- work, the next step is to confirm that the network interface is configured correctly on your host.

```
> ifconfig eth0
eth0      Link encap:Ethernet  HWaddr 04:01:62:e9:e1:01
          inet addr:128.199.97.25  Bcast:128.199.127.255  Mask:255.255.192.0
          inet6 addr: fe80::601:62ff:fee9:e101/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1005251 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1121461 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:437552891 (437.5 MB)  TX bytes:223384156 (223.3 MB)
```

### Is It on the Local Network?

```
> route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         128.199.64.1    0.0.0.0         UG    0      0        0 eth0
10.130.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth1
128.199.64.0    0.0.0.0         255.255.192.0   U     0      0        0 eth0
```

```
> vim /etc/network/interfaces
The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth1 eth0
iface eth0 inet static
        address 128.199.97.25
        netmask 255.255.192.0
        gateway 128.199.64.1
        dns-nameservers 8.8.8.8 8.8.4.4
iface eth1 inet static
        address 10.130.22.71
        netmask 255.255.0.0
```

```
pint -c 5 128.199.64.1
```

### Is DNS working?

```
> nslookup [host name]
```

### Can I route to the remote Host?

```
traceroute 10.1.2.5
```

### Is the Remote Port Open?

```
> telnet 10.1.2.5 80
> nmap -p 80 10.1.2.5 (can detect firewalls)
```

## Troubleshoot Slow Networks
* DNS issues
* Find the Network Slowdown with traceroute
* Find What is Using Your Bandwidth with iftop


## Solving DNS Server Issues

### DNS client troubleshooting

```
> nslookup www.naver.com 8.8.8.8
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
www.naver.com	canonical name = www.naver.com.nheos.com.
Name:	www.naver.com.nheos.com
Address: 125.209.222.141
Name:	www.naver.com.nheos.com
Address: 125.209.222.142
```

Although you could add +short to the end of the dig command to just get the IP address, dig gives us a lot of extra info that is useful for troubleshooting. For instance, it tells us the two name servers for example.net were named ns1 and ns2, and it also gives us their IP addresses. We will use this sort of extra information later when we troubleshoot DNS server issues.

```
> dig www.naver.com @8.8.8.8

; <<>> DiG 9.9.5-3ubuntu0.3-Ubuntu <<>> www.naver.com @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 46796
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.naver.com.			IN	A

;; ANSWER SECTION:
www.naver.com.		21495	IN	CNAME	www.naver.com.nheos.com.
www.naver.com.nheos.com. 79	IN	A	125.209.222.141
www.naver.com.nheos.com. 79	IN	A	125.209.222.142

;; Query time: 4 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Aug 21 23:44:24 EDT 2015
;; MSG SIZE  rcvd: 108
```

### DNS server troubleshooting

```
> dig www.naver.com +trace

; <<>> DiG 9.9.5-3ubuntu0.3-Ubuntu <<>> www.naver.com +trace
;; global options: +cmd
.			20741	IN	NS	b.root-servers.net.
.			20741	IN	NS	d.root-servers.net.
.			20741	IN	NS	g.root-servers.net.
.			20741	IN	NS	h.root-servers.net.
.			20741	IN	NS	l.root-servers.net.
.			20741	IN	NS	f.root-servers.net.
.			20741	IN	NS	c.root-servers.net.
.			20741	IN	NS	m.root-servers.net.
.			20741	IN	NS	a.root-servers.net.
.			20741	IN	NS	k.root-servers.net.
.			20741	IN	NS	i.root-servers.net.
.			20741	IN	NS	j.root-servers.net.
.			20741	IN	NS	e.root-servers.net.
.			20741	IN	RRSIG	NS 8 0 518400 20150831170000 20150821160000 1518 . kSuj56VD2fE25WdlZOsa3C4szBiGXSnDU9zEzyrOqTZAIqCQSfWV8vPK XjO4tqWSXQ6r2jNSUZfpCFm4JyKQsr5+Cl7Jlzt//BnVtjTeCAU8g+HL YQKCgfKZbxGMmqwEKIkD/brBZuB1G0mZz2CyWU3CsSiTR3RJ29rODE+s srQ=
;; Received 397 bytes from 8.8.8.8#53(8.8.8.8) in 64 ms

com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20150831170000 20150821160000 1518 . VRQ2AF21Mmxv/WnyhUBLrEIwW/9FDH9r2YHLX0GNp/5p4dI9aMQxNmHC hPDfl7vseTTRXbiBIknKn9pXP572W6ZNAGxROnrXojof9apMP+rzSfpo 39+Ob5u8XbnsqxGVn6cx19PH0+efDBhIfjDo6fio5NkQRV4psF2LpbVQ Y3I=
;; Received 737 bytes from 128.63.2.53#53(h.root-servers.net) in 306 ms

naver.com.		172800	IN	NS	ns2.naver.com.
naver.com.		172800	IN	NS	ns1.naver.com.
naver.com.		172800	IN	NS	ns3.naver.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0QFMDQRCSRU0651QLVA1JQB21IF7UR NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20150828045129 20150821034129 35864 com. GvkYbDiqExV3WTID3sY12eZrs2Rjtr+W4bvLylq8qz9ADQwQSJMrBOTG VYr76lALv/cXlE2IfCx6yuWRub129DKE0KfDcJKZzcqeHzJ4wwhTBlwX 8aupzlv97lOObfUjlb9ydDTZnu5yNJYkJq1CIEvzh5gIgDQ3NMVoaR3d T20=
6P7D0HNA1HQB20771H82P7UN3VJVP396.com. 86400 IN NSEC3 1 1 0 - 6P7I2VIGGGGQCOUHR09DQO12PJ6PPIT1 NS DS RRSIG
6P7D0HNA1HQB20771H82P7UN3VJVP396.com. 86400 IN RRSIG NSEC3 8 2 86400 20150828075207 20150821064207 35864 com. S48iveEXeH7B04oE96aKwxQRS32yq1t2+M2uCZUzBC4n89UQYXX3l0R5 V4yDiJBdWyroFGwZ7/500Y1M1VJM4Ra3UhxeglDiz8n8nTfgiLwQfszJ rW0pX4rfwnNhM3H60Ovfv6J2Jd8L6CFJh75327G5TTyXnHcdIRvE6iwo Ezw=
;; Received 629 bytes from 192.26.92.30#53(c.gtld-servers.net) in 266 ms

www.naver.com.		21600	IN	CNAME	www.naver.com.nheos.com.
;; Received 76 bytes from 61.247.195.250#53(ns1.naver.com) in 95 ms
```

#### Recursive Name Server Problems

On Linux machines these name servers are configured in **/etc/resolv.conf**.

#### When Updates Don't Take

**DNS Caching and TTL** Although DNS caching can be quite handy, one problem for DNS admin- istrators is that not every DNS server out there honors your TTL. In fact, some ISPs have been known to completely disregard TTLs that are too low, to help reduce the load on their DNS servers. Because of this, even though you may have a TTL of only a few minutes, a DNS change you make may take hours to show up on a DNS server that doesn’t obey it.

**Zone Syntax Erros** When you make a change to a zone file and then reload BIND, if it notices a syntax error in the file, it will simply disregard any changes to that particular zone file and stick to the records it has.

**Zone Transfer Issues** The last problem we’ll discuss that can cause a DNS change to not take is due to zone transfer issues. In your average DNS infrastructure, one DNS server is treated as the master for a particu- lar zone and the rest are configured as slaves. Any changes are made to the master and then are pushed out to any slaves that are configured. Zone transfer problems can be tricky to detect immediately because your master name server will report the new record; however, any queries to one or more slaves may report the old record. As a result, different clients may see different IP addresses at different times.


