

# Network & OS
> summary of questions


## Network

### Network basic
#### Default Gateway
the router for the subnetwork you're on
looking for target in local network, if it is not there, then ask the default gatewary to find target (maybe outside)

#### Subnet Mask
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

### OSI Layer 7

Layer|Name|
-----|----|
7|application
6|presentation
5|session
4|transport
3|network
2|data link
1|physical

***






1. physical
	- physical connection between devices:  a cable or a radio signal
	- **Ethernet**
2. data link
	- data unit : **frame**
	- end to end
	- at this layer, packets are encoded and decoded into bits
	- devided into tho sub layers: MAC layer and LLC(Logical Link Control) layer
	- MAC layer controls how a computer on the network gains access to the data and permission to transmit it. LLC layer controls frame synchronization, flow control and error checking
	- MAC address has 48 bits (FF-FF-FF-FF-FF-FF)
	- it can be segmented by VLAN (usually static VLAN)
	- map each port to which VLAN number (1,2,3,4~~)
	- to communicate with nodes in different VLAN, it needs L3 switch or router
	- **PPP, ATM, IEEE 802.5/802.2, IEEE802.3(이더넷)**
3. network
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
4. transport
	- data unit : **segment**
	- use **port** to know about application process
	- this layer provides transparent transfer of data between end systems, or hosts
	- is responsible for end-to-end error recovery and flow control
	- **TCP, UDP**
5. session
	- this layer establishes, manages and terminates connection between applications
	- it deals with session and connection coordination
	- **NFS, RPC, SQL**
6. presentation
	- the presentation layer works to transform data into the form that the application can accept
	- this layer formats and encrypts data to be sent across a network
	- **encrytion, ASCII, GIF, JPEG**
7. application
	- this layer supports application and end-user processes.
	- everything at this layer is application-specific
	- this layer provides application services for transfers, email, and network software services.
	- **HTTP, FTP**


### NAT
- Network Address Translation
- private IP와 public IP 간 주소를 변환해 주는 기술
- public IP vs private IP
- 3 mode

mode|desc
----|----
static|public IP를 private IP와 각각 정적으로 할당
pooled|사용 가능한 public IP 들을 pool로 관리해서 동적으로 할당 
PAT|public IP가 1개 일 때 , port forwarding 기술 사용

internal network have IP address range 192.168.1.0 - 255
what is your external IP address
router has NAT built into it.
default gateway is usally router

### DHCP 
dynamic host configuration protocol
dynamically assign the ip address to the client computer or network devices
DHCP server will provid certain TCP/IP information 

#### from DHCP it's going to get those 3 information
- Ip address
	- this is given dynamically
	- scope (Ex 192.168.1.100 ~ 192.168.1.200)
- subnet masking (static)
- default gateway (static) (can talk outside of network)

- reservation
- lease
	- lease it the client with the particular time

#### 4 step
- DHCP discovery
- DHCP offer
- DHCP request
- DHCP ack

#### Relay agent
It is not goot to have each DHCP server in every VLAN, so relay agent (usually L3 switch or router) send DHCP request to DHCP in different VLAN instead.

broadcast the request to anyone to ask where is DHCP server
-> DHCP server offers 

### DNS
domain name service maps domain name to IP
dynamically write the host name in IP address into DNS table
#### reverse DNS
opposite. maps ip adress to domain name
query to find the hostname

#### etc
your computer doesn't understand what cnn.com is.
cnn.com will return web page
this is basically how dns going to work for you
that will go to your local dns server
your local dns server also has dns server information
it has it's own record, but if it cant find, 

first look up in your dns server, and then primary dns server, finally the secondary dns server
that will go query


### ICMP (internet control message protocol)

#### ping
- packet internet grouper
- it's beyond TCP/UDP. just IP header + ICMP message
- **type** : request or reply
- **code** : (ex) message of the event
- codes
	- Request Timeout
	- Destination Unreachable

### HTTP
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

##### Body

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

#### when enter www.amazon.com in browser, what will happen?
http://www.quora.com/What-is-the-role-of-OSI-layers-when-we-open-a-webpage

### Router vs Switch
#### switch (Layer 2)
- layer 2 (data link)
- mac address (aka hardware address)
	- manufacturer no + social security no
- end to end
- 4 func.
	- flooding (broadcasting out to know where the destination is)
	- forwarding (send packet to the destination)
	- learning (which port is which pc **mac** )
	- filtering (after estblishing mac table, it do not broadcast packets)
it resides in OSI layer 2


#### router (layer 3)
- layer 3 (network)
- ip address 
- network to network

> layer 3 switch, router are different in the actual implementation. router is implemented in software, whereas a layer-3 switch perform the same operation using dedicated ASIC hardware.
	

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
###### RSA
#### SSL
###### Digital Certificates

open called "certs"
It contains basic things common

- Subject's name
- Expiration date
- Certificate issuer
- Digital signature from the certificate issuer
- the public key of the subject
	- 비공개키의 소유자는 비공개키를 이용해서 정보를 암호화 한 후에 공개키와 함께 암호화된 정보를 전송한다. 정보와 공개키를 획득한 사람은 공개키를 이용해서 암호화된 정보를 복호화 한다. 이 과정에서 공개키가 유출된다면 의도하지 않은 공격자에 의해서 데이터가 복호화 될 위험이 있다. 이런 위험에도 불구하고 비공개키를 이용해서 암호화를 하는 이유는 무엇일까? 그것은 이것이 데이터를 보호하는 것이 목적이 아니기 때문이다. 암호화된 데이터를 공개키를 가지고 복호화 할 수 있다는 것은 그 데이터가 공개키와 쌍을 이루는 비공개키에 의해서 암호화 되었다는 것을 의미한다. 즉 공개키가 데이터를 제공한 사람의 신원을 보장해주게 되는 것이다. 이러한 것을 전자 서명이라고 부른다
- CA (certificate authority) or Root Certificate
	- service provider need to buy certs from CA
- SSL certs has following information below
	- service information
	- service public key + public key encryption
- SSL certs is encrypted by CA private key which is only decrypted by CA public key
- Browser already knows CA list + CA's public key


http://security.stackexchange.com/questions/7360/why-cant-the-ssl-handshake-be-done-in-one-step?rq=1
https://opentutorials.org/course/228/4894


### ARP
- address resolution protocol
- in network, only 2 addresses exist, one is MAC address, another is IP address.

|physical|logical|
|--------|-------|
|MAC address|IP address|
|NIC(network interface card)|OS|

**Data link packet**

|Preemble|Target MAC address|Source MAC address|type|Data|FCS|
|--------|------------------|------------------|----|----|---|
|8 byte|6 byte|6 byte|2 byte|46 ~ 1500 byte|4 byte|

#### Process
- broadcast the request to find the mac address with target IP
- target device responses MAC address to the client
- register IP-MAC adress mapping in ARP table
- unicast the target with MAC address



### TCP/IP network model
1. network interface
2. internet
3. transport
4. application services

### TCP / UDP

http://www.quora.com/What-is-the-difference-between-TCP-and-UDP
comparision
- TCP
	- stateful
	- transmission control protocol (protocol no. 6)
	- it has 20 byte header
	- TCP can be thought of like having a telephone conversation
	- It has a similar establishment protocol, three-way-handshake. A client will send a SYN message to a server who is listening for these message. The server, if it decides to accept the request, will send back a SYN/ACK message, both acknowledgeing the previous request and requesting its own return channel fo communication
- UDP
	- user datagram protocol (protocol no. 17)
	- it has 8 byte header
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

### VPN

- tons of routers are sitting on the internet. you go throught any number of router
- virtual private networking allows you to connect target over the internet securily
- set up the tunnel between source and target
- inside tunnel all information is encrypted
- if there is hacker attack, the previous tunnel is corrupted, and rebuild new tunnel
- VPN is client server technology
- put IP adress with username, password
- you're not on LAN
- put the whole protocol into data area, so that it does not have to think of data converting  **Tunneling**


## OS (Linux)

### Application
#### iptables
https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html
iptables works inside internet & transport layers

##### channel
- input
	- all **incoming** packets are checked against the rules in this chain
- output
	- all **outcoming** packets are checked against the rules in this chain
	- emitted by the host. their destination is usually another host, but can be the same host via the loopback interface, so not all packets that go through OUTPUT are in fact outgoing
- forward
	- all packet being sent to another computer are checked against the rules in the chain
	- the packets that the host is merely routing



#### netstat

### Directory Structure
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

### linux file system

#### Ext2
- Ext2 stands for second extended file system.
- This was developed to overcome the limitation of the original ext file system.
- Ext2 does not have journaling feature.
- On flash drives, usb drives, ext2 is recommended, as it doesn’t need to do the over head of journaling.
- Maximum individual file size can be from 16 GB to 2 TB
- Overall ext2 file system size can be from 2 TB to 32 TB

#### Ext3
- third extended file system.
- The main benefit of ext3 is that it allows journaling.
- Journaling has a dedicated area in the file system, where all the  changes are tracked. When the system crashes, the possibility of file  system corruption is less because of journaling.
- Maximum individual file size can be from 16 GB to 2 TB
- Overall ext3 file system size can be from 2 TB to 32 TB
- There are three types of journaling available in ext3 file system.
- Journal – Metadata and content are saved in the journal.
- Ordered – Only metadata is saved in the journal. Metadata are  journaled only after writing the content to disk. This is the default.
- Writeback – Only metadata is saved in the journal. Metadata might be  journaled either before or after the content is written to the disk.
- You can convert a ext2 file system to ext3 file system directly (without backup/restore).

#### Ext4
- fourth extended file system.
- Supports huge individual file size and overall file system size.
- Maximum individual file size can be from 16 GB to 16 TB
- Overall maximum ext4 file system size is 1 EB (exabyte). 1 EB = 1024 PB (petabyte). 1 PB = 1024 TB (terabyte).
- Directory can contain a maximum of 64,000 subdirectories (as opposed to 32,000 in ext3)
- You can also mount an existing ext3 fs as ext4 fs (without having to upgrade it).
- Several other new features are introduced in ext4: multiblock  allocation, delayed allocation, journal checksum. fast fsck, etc. All  you need to know is that these new features have improved the  performance and reliability of the filesystem when compared to ext3.
- In ext4, you also have the option of turning the journaling feature “off”.

#### FAT
#### NTFS

### Raid

|Raid|Desc|
|----|----|
|0|Striping(sharding)<br>|
|1|Mirroring|
|2||
|3|byte-level striping with dedicated parity. <br>One disk for parity|
|4|block-level striping with dedicated parity .<br> One disk for parity|
|5|block-level striping with distributed parity|

- **RAID 0** Striping
- **RAID 1** Mirroring
- **RAID 3** byte-level parity is in dedicated parity. not commonly used in practice
- **RAID 4** block-level parity is in dedicated parity. 
- **RAID 5** parity information is distributed among the drives. Raid 5 need at least **3** disks.

- **RAID 0+1** creates a second striped set to mirror a primary striped set. The array continues to operate with one or more drives failed in the same mirror set, but if drives fail on both sides of the mirror the data on the RAID system is lost.
- **RAID 1+0** creates a striped set from a series of mirrored drives. The array can sustain multiple drive losses so long as no mirror loses all its drive

### Inodes
파일에 대한 index nodes
- i_blocks
- i_mode
- i_links_count
- i_uid
- i_gid
- i_atime
- i_ctime
- i_mtime

파일을 1개의 inode를 가짐
일반적으로 전체공간의 1%를 inode를 위해 할당
- run out of inodes
- ext3의 경우 inode의 개수를 설정 가능

### File System
#### Soft Link
- 하나는 실제 데이터 하나는 첫번째 파일의 포인터

#### Hard Link
- 두개의 파일이 하나의 inode를 가리킴
- 파일시스템간 불가, 디렉토리는 불가
- 하나를 삭제 하면 링크 갯수만 하나 감소
- . 와 .. 은 디렉토리 엔트리에 대한 하드링크

### LDAP ![](http://a2.mzstatic.com/us/r30/Purple1/v4/11/cb/b4/11cbb408-4352-013b-9849-f57209330153/icon256.png)
- LDAP (Lightweight Directory Access Protocol) is an application protocol for querying and modifying items in directory service providers like Active Directory, which supports a form of LDAP.

### Active Directory Service ![](http://a2.mzstatic.com/us/r30/Purple1/v4/11/cb/b4/11cbb408-4352-013b-9849-f57209330153/icon256.png)
- Active Directory is a database based system that provides authentication, directory, policy, and other services in a Windows environment
- Short answer: AD is a directory services database, and LDAP is one of the protocols you can use to talk to it.

### IPSEC
- ip network encryption protocol
- sa : security association
- **transport mode** encrypt all
- **tunnel mode** encrypt IP Packet's data section

## Case

- receive a 200 response code when navigating to the domain, but the page wont load, how do you troubleshoot?
- how to handle problems CPU 100%
- a client has a problem with his computer and he tells you that his IP is X.X.X.X. Diagnose the issue
- what if domain name could not be resolved in any name servers
