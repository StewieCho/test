# Operation System

## Basic
### TTY

- computer terminal
- getty, short for "get teletype", is a Unix program running on a host computer that manages physical or virtual terminals (TTYs). When it detects a connection, it prompts for a username and runs the 'login' program to authenticate the user
- background program has terminal, daemon don't.
- 유닉스 전문 용어로 tty는 단순히 읽기와 쓰기를 넘어 몇 가지 추가적인 명령어를 지원하는 디바이스 파일을 뜻합니다. 그리고 터미널은 사실상 이 tty와 동일한 의미로 사용됩니다. 일부 tty는 하드웨어 디바이스를 위해 커널이 제공하며 그 예로 키보드에서 들어오는 입력과 텍스트 모드 화면으로 나가는 출력, 시리얼 라인을 통해 전송되는 입출력이 있습니다. 그 외의 tty는 씬 커널 레이어를 통해 터미널 에뮬레이터라고 불리는 프로그램으로 제공되며 이런 tty를pseudo-tty로 부르기도 합니다. 터미널 에뮬레이터로는 Xterm(X 윈도우 시스템), Screen(프로그램과 다른 터미널 사이에 독립적인 계층을 제공), SSH(한 머신에서 프로그램으로 다른 머신의 터미널에 연결), Expect(스크립팅 터미널 인터랙션용) 등이 있습니다.

```
[yundream@huhu loging_server]$ ./my_server&
[yundream@huhu loging_server]$ ps -efjc
UID        PID  PPID  PGID   SID   CLS PRI STIME TTY          TIME CMD
yundream  4314  4219  4314  4175     -  30 15:36 ttyp0    00:00:00 ./my_server&

[yundream@huhu loging_server]$ ./my_server -D
[yundream@huhu loging_server]$ ps -efjc
UID        PID  PPID  PGID   SID   CLS PRI STIME TTY          TIME CMD
yundream  4319     1  4319  4319     -  30 15:37 ?        00:00:00 ./my_server
```


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

## BIND
- Berkeley Internet Name Domain



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
- Maximum individual file size can be from **16 GB to 2 TB**
- Overall ext2 file system size can be from **2 TB to 32 TB**

### Ext3
- third extended file system.
- The main benefit of ext3 is that it allows **journaling**.
- **Journaling has a dedicated area in the file system, where all the changes are tracked**. When the system crashes, the possibility of file  system corruption is less because of journaling.
- Maximum individual file size can be from 16 GB to 2 TB
- Overall ext3 file system size can be from 2 TB to 32 TB
- There are three types of journaling available in ext3 file system.
- **Journal** ? Metadata and content are saved in the journal.
- **Ordered** ? Only metadata is saved in the journal. Metadata are  journaled only after writing the content to disk. **This is the default.**
- **Writeback** ? Only metadata is saved in the journal. Metadata might be  journaled either before or after the content is written to the disk.
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
	- extents (instead of block mapping, adjacent block mappinrg, reduce fragment)
	- backward compatibility
	- persistent pre-allocation
	- delayed allocation (do not allocate before write data. prevent fragmentation)
	- journal checksumming (more reliable journing)
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
a file in the file system is basically a link to an inode. 

- A hard link then just creates another file with a link to the same underlying inode. When you delete a file, it removes one link to the underlying inode. The inodes is only deleted when all links to the inode have been deleted.

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
