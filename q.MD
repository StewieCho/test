

# Network & OS
> summary of questions

[TOC]

## Network

### Network basic
#### Default Gateway
#### Subnet Mask

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

### DHCP 
###### IP address
- ip, gateway, subnet mask

#### DNS

### ICMP (internet control message protocol)
#### ping
#### tracert

### routing
#### static
static routing은 관리자가 지정한 라우팅 경로에 의해서만 라우팅이 이루어짐
#### dynamic
- dynamic routing은 RIP, OSPF, BGP를 이용해서 서로 정보를 교환하여 동적인 라우팅 테이블을 생성
- 상대적으로 느리나 가변적인 네트워크 환경에서 사용하기 좋다.

### HTTP
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

#### router operation concept

#### public key / private key encryption

#### OSI Layer 7
1. physical
	- physical connection between devices:  a cable or a rado signal
	- **Ethernet**
2. data link
	- at this layer, data packets are encoded and decoded into bits
	- devided into tho sub layers: MAC layer and LLC(Logical Link Control) layer
	- MAC layer controls how a computer on the network gains access to the data and permission to transmit it. LLC layer controls frame synchronization, flow control and error checking
	- **PPP, ATM, IEEE 802.5/802.2**
3. network
	- transmitting data between devices on different networks
	- this layer provides switching and routing for transmitting data from node to node
	- routing, forwarding, addressing, internetworking, packet sequencing  are functions of this layer
	- **IP**
4. transport
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

#### TCP/IP network model
1. network interface
2. internet
3. transport
4. application services


#### Active Directories


#### TCP / UDP
http://www.quora.com/What-is-the-difference-between-TCP-and-UDP
- TCP
	- TCP can be thought of like having a telephone conversation
	- It has a similar establishment protocol, three-way-handshake. A client will send a SYN message to a server who is listening for these message. The server, if it decides to accept the request, will send back a SYN/ACK message, both acknowledgeing the previous request and requesting its own return channel fo communication
- UDP

###### TCP 3 way handshaking
###### TCP flags
###### TCP sequence

#### SSL

## OS (Linux)

### Application
#### iptables
- channel

#### netstat

### Common Ports
Port|Apps
----|----
53|DNS
110|POP3

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
Raid 0 : 데이터를 물리적으로 나눔 (sharding) 속도는 좋으나 안정성 떨어짐
Raid 1 : Mirroring, 백업용으로 디스크를 하나 더 두는 컨셉
Raid 3 : 데이터 오류 체크 기술로 Raid 0 의 안정성 보완

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

### LDAP

## Case

- receive a 200 response code when navigating to the domain, but the page wont load, how do you troubleshoot?
- how to handle problems CPU 100%
- a client has a problem with his computer and he tells you that his IP is X.X.X.X. Diagnose the issue
