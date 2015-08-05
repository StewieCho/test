# Network
**OSI Layer 7**

Layer|Name|
-----|----|
7|application
6|presentation
5|session
4|transport
3|network
2|data link
1|physical

![](http://qph.is.quoracdn.net/main-qimg-abd45b991837317f9bd873a7f14ab0eb?convert_to_webp=true)

---------------------------------------------------------------------------------------------------------
## 1. Physical Layer
- physical connection between devices:  a cable or a radio signal
- **Ethernet**

---------------------------------------------------------------------------------------------------------
## 2. Data link Layer
- data unit : **frame**
- end to end
- at this layer, frame(including packet) are encoded and decoded into bits
- devided into tho sub layers: MAC layer and LLC(Logical Link Control) layer
- MAC layer controls how a computer on the network gains access to the data and permission to transmit it. LLC layer controls frame synchronization, flow control and error checking
- it can be segmented by VLAN (usually static VLAN)
- map each port to which VLAN number (1,2,3,4~~)
- to communicate with nodes in different VLAN, it needs L3 switch or router
- **PPP, ATM, IEEE 802.5/802.2, IEEE802.3(이더넷)**

### MAC Address
- MAC address has 48 bits (FF-FF-FF-FF-FF-FF)
- menufacturer no + social security no

### Data link frame
|Preamble|Target MAC address|Source MAC address|type|Data|FCS|
|--------|------------------|------------------|----|----|---|
|8 byte|6 byte|6 byte|2 byte|46 ~ 1500 byte|4 byte|

### ARP
- address resolution protocol
- in network, only 2 addresses exist, one is MAC address, another is IP address.

|physical|logical|
|--------|-------|
|MAC address|IP address|
|NIC(network interface card)|OS|

#### Process
- broadcast the request to find the mac address with target IP
- target device responses MAC address to the client
- register IP-MAC adress mapping in ARP table
- unicast the target with MAC address

### Switch
- layer 2 (data link)
- end to end
- there is a map from MAC address to physical ports
- 4 func.
	- flooding (broadcasting out all physical port but the source node)
	- forwarding (send packet to the destination)
	- learning (which port is which pc **mac** )
	- filtering (after estblishing mac table, it do not broadcast packets)
	- aging (like MAC table cache)

---------------------------------------------------------------------------------------------------------
## 3. Network Layer
- data unit : **packet**
- network to network
- transmitting data between devices on **different networks**
- this layer provides switching and routing for transmitting data from node to node
- routing, forwarding, addressing, internetworking, packet sequencing  are functions of this layer
- **network address** : 192.168.1.1 (255.255.255.0) => 192.168.1.0
- broadcast address (local broadcast address) : 192.168.1.1 (255.255.255.0) => 192.168.1.255 , is used to broadcast all node in same network
- broadcast address (limited broadcast address) : 255.255.255.255 , is used to broadcast all node in same network if client does not know its own IP address (ex: DHCP)
- loop-back address : 127.0.0.1/32 (1 octet is 127, rest of them do care)
- source, target MAC address in IP packet changes in every network
- **IP**

### Default Gateway
the router for the subnetwork you're on
looking for target in local network, if it is not there, then ask the default gatewary to find target (maybe outside)

### Subnet Mask
it tells what part of IP adress is the network identifier or device identifier. 
```
class A subnet - 255.0.0.0
class B subnet - 255.255.0.0
class C subnet - 255.255.255.0

192.168.1.X
255.255.255.0
network part - 192.168.1
device part - 1
```

if you wanna devide that into two network, set the subnet mask to 255.255.255.128 etc. Network A do not connect to network B unless there is router.

**router connects different subnet**

### ICMP (internet control message protocol)
ICMP is one of the main protocol of Internet Protocol suite. It is used by network devices, like router, to send error messages indicating, for example, that a requested service is not available or that a host or router could not be reached.
#### ping
- packet internet grouper
- it's beyond TCP/UDP. just IP header + ICMP message
- **type** : request or reply
- **code** : (ex) message of the event
- codes
	- Request Timeout
	- Destination Unreachable

### Router
router is a networking device that forwards data packets between computer networks
- layer 3 (network)
- ip address 
- network to network

> layer 3 switch, router are different in the actual implementation. router is implemented in software, whereas a layer-3 switch perform the same operation using dedicated ASIC hardware.

### VPN
VPN stands for vitual private network, which extends a private network acress a public network such as the Internet.

- tons of routers are sitting on the internet. you go throught any number of router
- virtual private networking allows you to connect target over the internet securily
- set up the tunnel between source and target
- inside tunnel all information is encrypted
- if there is hacker attack, the previous tunnel is corrupted, and rebuild new tunnel
- VPN is client server technology
- put IP adress with username, password
- you're not on LAN
- put the whole protocol into data area, so that it does not have to think of data converting  **Tunneling**

### IPSEC
IPSEC is internet protocol security. that is a protocol suite for securing IP communication by authenticating and encrypting each IP packet of a communication session.
- ip network encryption protocol
- sa : security association
- **transport mode** encrypt all
- **tunnel mode** encrypt IP Packet's data section

---------------------------------------------------------------------------------------------------------
## 4. Transport Layer
- data unit : **segment**
- use **port** to know about application process
- this layer provides transparent transfer of data between end systems, or hosts
- is responsible for end-to-end error recovery and flow control
- **TCP, UDP**

### NAT
Network address translation is a methodology of remapping one internal IP address into external another by modifying network address information in IP datagram.
- Network Address Translation
- private IP와 public IP 간 주소를 변환해 주는 기술
- public IP vs private IP
- 3 mode
	- **one-to-many** : map multiple private hosts to one publicly exposed IP. Port address translation (PAT) allows many internal hosts to share a single external IP address.
	- **one-to-one** : basic
- **NAT loopback** : the access of a service via the public IP address from inside the local network
- pooled mode, static mode
- default gateway is usually router which has NAT built into it

### DHCP
dynamic host configuration protocol is a network protocol to dynamically assign the ip address to the client computer or network devices.DHCP server will provid certain TCP/IP information. **/etc/dhcpd.conf**

#### 4 step
- **DHCP discovery** : using broadcast address(255.255.255.255) request all nodes to ask where the DHCP server is
- **DHCP offer** : one or more DHCP server replies the offer including IP, subnet mask, default gateway to a client
- **DHCP request** : in reponse of the DHCP offer, the client replies with a first DHCP server
- **DHCP ack** : When the DHCP server receives the DHCPREQUEST message from the client, DHCP server sends a DHCPACK packet includes the lease duration and any other configuration information that the client might have requested

#### 3 key information
- Ip address
	- this is given dynamically
	- scope (Ex 192.168.1.100 ~ 192.168.1.200)
- subnet masking (static)
- default gateway (static) (can talk outside of network)


#### Relay agent
It is not goot to have each DHCP server in every VLAN, so relay agent (usually L3 switch or router) send DHCP discovery to DHCP server in different VLAN instead.


### DNS
DNS is domain name system, that is a distributed naming system for resource connected to the Internet or a private network. usually domain name system maps domain name to IP. it dynamically writes the host name in IP address into DNS table. Related files are /etc/host.conf, /etc/hosts
![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b1/Domain_name_space.svg/1200px-Domain_name_space.svg.png)

#### Address resolution mechanism
![](https://upload.wikimedia.org/wikipedia/commons/thumb/7/77/An_example_of_theoretical_DNS_recursion.svg/1200px-An_example_of_theoretical_DNS_recursion.svg.png)


#### reverse DNS
opposite. maps ip adress to domain name
query to find the hostname

#### Simply speaking
your computer doesn't understand what cnn.com is. so when you put cnn.com, at first that will go to your local dns server. If it doesn't know, your local dns server also has dns server information. like I said, first look up in your dns server, and then primary dns server, finally the secondary dns server
that will go query. if you get IP address, then try to connect cnn.com, then cnn.com will return web page.

#### When enter www.amazon.com in browser, what will happen?
- I typically type an URL in browser, browser uses HTTP protocol and made packet.
- the browser gives the HTTP packet to TCP and find the name of www.amazon.com in DNS server
- this is asking the recursive DNS servers which is your ISP's recursive DNS
- then, ask the root nameservers
- then, ask the TLD(top-level domain) nameservers
- then ask the authoritative DNS servers
- and retrieve the record then receive the answer.


### TCP/IP network model
1. network interface
2. internet
3. transport
4. application services

![](http://qph.is.quoracdn.net/main-qimg-ff56b851ebf652a16ee47dff4fcf2349?convert_to_webp=true)

### TCP / UDP	

- **TCP**
	- transmission control protocol (protocol no. 6)
	- it provides stateful, reliable, ordered and error-checked delivery of a stream
	- it has 20 byte header
	- TCP can be thought of like having a telephone conversation
	- It has a similar establishment protocol, three-way-handshake. A client will send a SYN message to a server who is listening for these message. The server, if it decides to accept the request, will send back a SYN/ACK message, both acknowledgeing the previous request and requesting its own return channel fo communication
- **UDP**
	- user datagram protocol (protocol no. 17)
	- UDP uses a simple connectionless transmission model. No handshaking dialogue.
	- there is no guarantee of delivery, ordering, or duplicate protection
	- it has 8 byte header (minimal message-oriented)
	- accept data after checking if data is ok with header length & checksum

#### TCP flags
control bit (6 bits)

URG|ACK|PSH|RST|SYN|FIN
---|---|---|---|---|---
urgent|acknowledgent|push|reset|synchronize|finish

#### TCP 3 way handshaking
##### flag
- SYN -> SYN/ACK -> ACK

##### sequence
- SYN (node1 seq 0)
- => SYN/ACK (node2 seq 100)
- => ACK (seq 1 , ack 101) which means seq + 1 and ack +1

#### TCP connection termination
##### flag
- FIN/ACK -> ACK -> FIN/ACK -> ACK

#### Common Ports
Range|Name|Desc
-----|----|----
0~1023|System Ports(Well-known Ports)|General Application
1024~49151|UserPorts|
49152~65535|Dynamic or Private Ports|

Port|Apps
----|----
22|SSH
23|telnet
25|SMTP
53|DNS
67|DHCP server
68|DHCP client
110|POP3

http://www.quora.com/What-is-the-difference-between-TCP-and-UDP

---------------------------------------------------------------------------------------------------------
## 5. session
- this layer establishes, manages and terminates connection between applications
- it deals with session and connection coordination
- **NFS, RPC, SQL**

---------------------------------------------------------------------------------------------------------
## 6. presentation
- the presentation layer works to transform data into the form that the application can accept
- this layer formats and encrypts data to be sent across a network
- **encrytion, ASCII, GIF, JPEG**

---------------------------------------------------------------------------------------------------------
## 7. application
- this layer supports application and end-user processes.
- everything at this layer is application-specific
- this layer provides application services for transfers, email, and network software services.
- **HTTP, FTP**

### HTTP
- hypertext transfer protocol is an application protocol that is base of data communication for the WWW.
- it resides in layer 7

#### HTTP 1.0 / 1.1
- **HTTP 1.0** : every request make connection
- **HTTP 1.1** : reuse connection

#### HTTP message
##### Header
- **Connection: Keep-Alive**
- **Accept-Encoding, Content-Encoding**
- **Content-type**
- **User-Agent**
- **Cookie**

#### Response Code
code|result
----|------
1XX|informational
2XX|Successful
200|OK
204|No Content
3XX|Redirection
4XX|Client Error
400|Bad Request
401|Unauthorized(The request requires user authentication)
403|Forbidden(Authorization)
404|Not Found
500|Internal Server Error

### Cryptography (Encryption) with SSL
#### Symmetric cryptosystems
use the same key for decoding and encoding
cipher

#### Asymmetric cryptosystems
use the different key for decoding and encoding
##### public-key cryptography
both keys can decode & encode.
In EC2 SSH, the user has the private key. EC2 only has public key.
In public-key authentication, the client gives the public key to the server. and put that file in UserHome/.ssh authorized_keys (permission 600)
##### RSA
- RSA is one of the first practical public-key cryptosystems
- key generation , encryption, decryption

#### Digital Certificates

open called "certs"
It contains basic things common

- Subject's name
- Expiration date
- Certificate issuer
- Digital signature from the certificate issuer
- the public key of the subject
- CA (certificate authority) or Root Certificate
	- service provider need to buy certs from CA
- SSL certs has following information below
	- service information
	- service public key + public key encryption
- SSL certs is encrypted by CA private key which is only decrypted by CA public key
- Browser already knows CA list + CA's public key

#### SSL Handshake
![](https://docs.oracle.com/cd/E14571_01/core.1111/e10105/img/ssl_hello.gif)
- client hello (w/ random key)
- server hello (w/ random key)
- certificate (decode certs with public key that is already in browser. if it is possible, that mean that certs are provided by CA^certificate_authority^)
- with it's random key + server's random key the client makes pre-master key and then exchange it with the server after encrypt that key by the public key given by the server

http://security.stackexchange.com/questions/7360/why-cant-the-ssl-handshake-be-done-in-one-step?rq=1
https://opentutorials.org/course/228/4894



---------------------------------------------------------------------------------------------------------

# Operation System

## Application
### Iptables
Iptables is used to a simple firewall in linux. iptables works inside internet & transport layers

#### policy
- **Accept** : this is used to explicitly pass through as long as no target rule apply.
- **Reject** : this is used to send back an error packet in reponse to the matched packet.otherwise it is equivalent to DROP
- **Drop** : this policy will stop a connection to a host without any communication unless there is a target rule that applied

#### table
- filter
- nat
- mangle

#### channel
- input
	- all **incoming** packets are checked against the rules in this chain
- output
	- all **outcoming** packets are checked against the rules in this chain
	- emitted by the host. their destination is usually another host, but can be the same host via the loopback interface, so not all packets that go through OUTPUT are in fact outgoing
- forward
	- all packet being sent to another computer are checked against the rules in the chain
	- the packets that the host is merely routing

#### command example
```
iptables -A INPUT -i lo -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT
```

https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html

### Linux configuration files
#### Access Files
- /etc/hosts : List hosts for name lookup use that are locally required.
- /etc/host.conf
```
multi on
```
- /etc/hosts.allow
```
#
# hosts.allow	This file contains access rules which are used to
#		allow or deny connections to network services that
#		either use the tcp_wrappers library or that have been
#		started through a tcp_wrappers-enabled xinetd.
#
#		See 'man 5 hosts_options' and 'man 5 hosts_access'
#		for information on rule syntax.
#		See 'man tcpd' for information on tcp_wrappers
#
ALL:182.192.71.
ALL:70.50.122.
ALL:70.50.
ALL:70.50.182.
```
- /etc/hosts.deny
```
#
# hosts.deny	This file contains access rules which are used to
#		deny connections to network services that either use
#		the tcp_wrappers library or that have been
#		started through a tcp_wrappers-enabled xinetd.
#
#		The rules in this file can also be set up in
#		/etc/hosts.allow with a 'deny' option instead.
#
#		See 'man 5 hosts_options' and 'man 5 hosts_access'
#		for information on rule syntax.
#		See 'man tcpd' for information on tcp_wrappers
#
ALL:ALL
```
#### Booting and login/logout
- /etc/rc.d/rc.local
```
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
touch /var/lock/subsys/local
```

#### File System
- /etc/fstab

#### System Administration
- /etc/group
- /etc/passwd
```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
.
.
```
#### Networking
- /etc/resolv.conf
```
nameserver 203.241.135.130
```

- /etc/named.conf
- /etc/networks
```
default 0.0.0.0
loopback 127.0.0.0
link-local 169.254.0.0
```
- /etc/sysconfig/nework
```
NETWORKING=yes
NETWORKING_IPV6=yes
HOSTNAME=bdphdm01
```
- /etc/services
```
# service-name  port/protocol  [aliases ...]   [# comment]
tcpmux          1/tcp                           # TCP port service multiplexer
tcpmux          1/udp                           # TCP port service multiplexer
rje             5/tcp                           # Remote Job Entry
rje             5/udp                           # Remote Job Entry
```

#### System Commands
#### Daemons

- /etc/sudoer
```
.
.
.
## Allow root to run any commands anywhere 
root	ALL=(ALL) 	ALL
## Allows people in group wheel to run all commands
# %wheel	ALL=(ALL)	ALL

```



## Directory Structure
Directory|Desc
---------|----
/bin|Common programs, shared by the system
/dev|Contains references to all the CPU peripheral hardware
/etc|Most important system configuration files are in /etc
/home|Home directories of the common users
/lib|Library files, includes files for all kinds of programs needed by the system and the users
/mnt|Standard mount point for external file systems, e.g. a CD-ROM or a digital camera.
/opt|Typically contains extra and third party software.
/proc|A virtual file system containing information about system resources
/sbin|Programs for use by the system and the system administrator
/usr|Programs, libraries, documentation etc. for all user-related programs
/var|Storage for all variable files and temporary files created by users, such as log files, the mail queue

## linux file system

### Ext2
- Ext2 stands for second extended file system.
- This was developed to overcome the limitation of the original ext file system.
- Ext2 does not have journaling feature.
- On flash drives, usb drives, ext2 is recommended, as it doesn’t need to do the over head of journaling.
- Maximum individual file size can be from 16 GB to 2 TB
- Overall ext2 file system size can be from 2 TB to 32 TB

### Ext3
- third extended file system.
- The main benefit of ext3 is that it allows journaling.
- Journaling has a dedicated area in the file system, where all the  changes are tracked. When the system crashes, the possibility of file  system corruption is less because of journaling.
- Maximum individual file size can be from 16 GB to 2 TB
- Overall ext3 file system size can be from 2 TB to 32 TB
- There are three types of journaling available in ext3 file system.
- Journal ? Metadata and content are saved in the journal.
- Ordered ? Only metadata is saved in the journal. Metadata are  journaled only after writing the content to disk. This is the default.
- Writeback ? Only metadata is saved in the journal. Metadata might be  journaled either before or after the content is written to the disk.
- You can convert a ext2 file system to ext3 file system directly (without backup/restore).

### Ext4
- fourth extended file system.
- Supports huge individual file size and overall file system size.
- Maximum individual file size can be from 16 GB to 16 TB
- Overall maximum ext4 file system size is 1 EB (exabyte). 1 EB = 1024 PB (petabyte). 1 PB = 1024 TB (terabyte).
- Directory can contain a maximum of 64,000 subdirectories (as opposed to 32,000 in ext3)
- You can also mount an existing ext3 fs as ext4 fs (without having to upgrade it).
- Several other new features are introduced in ext4: multiblock  allocation, delayed allocation, journal checksum. fast fsck, etc. All  you need to know is that these new features have improved the  performance and reliability of the filesystem when compared to ext3.
	- large file system
	- extents
	- backward compatibility
	- persistent pre-allocation
	- delayed allocation
	- journal checksumming
	- multiblock allocation

- In ext4, you also have the option of turning the journaling feature “off”.


### FAT
### NTFS

## Raid

- **RAID 0** Striping
- **RAID 1** Mirroring
- **RAID 3** byte-level parity is in dedicated parity. not commonly used in practice
- **RAID 4** block-level parity is in dedicated parity.
- **RAID 5** parity information is distributed among the drives. Raid 5 need at least **3** disks.

- **RAID 0+1** creates a second striped set to mirror a primary striped set. The array continues to operate with one or more drives failed in the same mirror set, but if drives fail on both sides of the mirror the data on the RAID system is lost.
- **RAID 1+0** creates a striped set from a series of mirrored drives. The array can sustain multiple drive losses so long as no mirror loses all its drive

## Inodes
a file in the file system is basically a link to an inode. A hard link then just creates another file with a link to the same underlying inode. When you delete a file, it removes one link to the underlying inode. The inodes is only deleted when all links to the inode have been deleted.

A symbolic(soft) link is a link to the another name in the file system.

**Note**: Hard links are only valid within the same File System. Symbolic links can span file systems as they are simply the name of another file.

An inode is metadata of the data. whenever a user or a program needs access to a file, the operating system first searches for the exact and unique inode (inode number), in a table called as an inode table.

- i_blocks
- i_mode
- i_links_count
- i_uid
- i_gid
- i_atime
- i_ctime
- i_mtime

generally, 1% of disk is for inode. It's possible to check inode usage. There is no way to expand inode quantity without reboot.
```
df -ih
```

### Soft Link
- a soft link map from a directory entry to another directory entry.

### Hard Link
- a hard link is a mapping from a directory entry to an inode

## LDAP
- LDAP (Lightweight Directory Access Protocol) is an application protocol for querying and modifying items in directory service providers like Active Directory, which supports a form of LDAP.

## Active Directory Service
- Active Directory is a database based system that provides authentication, directory, policy, and other services in a# Network
- Short answer: AD is a directory services database, and LDAP is one of the protocols you can use to talk to it.
---------------------------------------------------------------------------------------------------------

# Case

- receive a 200 response code when navigating to the domain, but the page wont load, how do you troubleshoot?
- how to handle problems CPU 100%
- a client has a problem with his computer and he tells you that his IP is X.X.X.X. Diagnose the issue
- what if domain name could not be resolved in any name servers
- Short answer: AD is a directory services database, and LDAP is one of the protocols you can use to talk to it.

