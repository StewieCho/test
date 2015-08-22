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

### File Authorization
```
> umask
0002

> umask 077
```

SetUID
- when it is executed, it runs with user permission
```bash
> chmod 4755 [File]
> ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 30768 Feb 22  2012 /usr/bin/passwd
```

SetGID
- when it is executed, it runs with group permission
```bash
> chmod 2755 [File]
```

stick Bit
- even though it has 777 permision so that it looks like other can erase, only user can delete it
```bash
drwxrwxrwt. 109 root root 610304 Aug 20 16:27 tmp/
> chmod 1777 [FILE]
> ll
total 8
-rwxrwxrwx. 1 hadoop hadoop    0 Aug 20 16:34 stickbit*
> rm -rf stickbit 
rm: cannot remove `stickbit': Operation not permitted
```

## Tools
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

```
**> iptables -L (ACCEPT)**
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

**> iptables -L (DROP)**
Chain INPUT (policy DROP)
target     prot opt source               destination

Chain FORWARD (policy DROP)
target     prot opt source               destination

Chain OUTPUT (policy DROP)
target     prot opt source               destination

**> iptables -L (80 port DROP)**
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0      tcp dpt:80

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html

#### Logging
```
> iptables -N LOGGING
> iptables -A INPUT -j LOGGING
> iptables -A INPUT -P ICMP -d 192.168.2.121 -j ACCEPT
> iptables -A LOGGING -j log
```

### at
```bash
> cat /etc/al.allow (users to prohibit to use at)
> cat /etc/al.deny (users to be allowed to use at)
```

### crontab
```bash
> crontab -e
```

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

```bash
> swapon -s
Filename				Type		Size	Used	Priority
/dev/sdb1                               partition	133119992	10016	-1
> cat /etc/fstab

# /etc/fstab
# Created by anaconda on Thu Aug 28 15:19:06 2014
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/vg_bdpapp01-LogVol03 /                       ext4    defaults        1 1
UUID=43cd7dd3-f20a-4084-ad2a-626125ac8dbf /boot                   ext4    defaults        1 2
/dev/mapper/vg_bdpapp01-LogVol00 /home                   ext4    defaults        1 2
/dev/mapper/vg_bdpapp01-LogVol02 /usr                    ext4    defaults        1 2
/dev/mapper/vg_bdpapp01-LogVol01 /var                    ext4    defaults        1 2
UUID=98e7d2af-00e5-4517-bb83-de03871cbcec swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
```

- mkfs
```bash
> cat /etc/mke2fs.conf 
[defaults]
	base_features = sparse_super,filetype,resize_inode,dir_index,ext_attr
	blocksize = 4096
	inode_size = 256
	inode_ratio = 16384
[fs_types]
	ext3 = {
		features = has_journal
	}
	ext4 = {
		features = has_journal,extent,huge_file,flex_bg,uninit_bg,dir_nlink,extra_isize
		inode_size = 256
	}
	ext4dev = {
		features = has_journal,extent,huge_file,flex_bg,uninit_bg,dir_nlink,extra_isize
		inode_size = 256
		options = test_fs=1
	}
	small = {
		blocksize = 1024
		inode_size = 128
		inode_ratio = 4096
	}
	floppy = {
		blocksize = 1024
		inode_size = 128
		inode_ratio = 8192
	}
	news = {
		inode_ratio = 4096
	}
	largefile = {
		inode_ratio = 1048576
		blocksize = -1
	}
	largefile4 = {
		inode_ratio = 4194304
		blocksize = -1
	}
	hurd = {
	     blocksize = 4096
	     inode_size = 128
	}
```

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

#### Hosts Allow & Deny

```
/etc/hosts.deny
ALL:ALL
```

```
/etc/hosts.allow
httpd       : ALL
sshd        : 192.168.56.101
sendmail        : ALL
mysql        : ALL
```

deny가 ALL로 되어 있다 해도 allow 에 세부 application 별 IP 를 설정할 수 있다.

#### syslog
* /var/log/syslog 에서 확인 가능
* 설정 파일은 /etc/rsyslog.conf

#### cron
cron 데몬이 기본으로 읽어오는 것은 3가지

* /etc/crontab
* /etc/cron.d
* /var/spool/cron/crontab

```
> crontab -l (list for indivisual users)
> crontab -e (edit)
```

/etc/cron.deny, /etc/cron.allow 를 통해 통제 가능



## BIND
- Berkeley Internet Name Domain

## PAM

pluggable authentication module. 특정 프로그램 인증이 PAM 을 지원하는지 알아보기 위해서는 다음과 같이 입력

```
root@ubuntu-test:/etc# ldd /usr/bin/passwd
	linux-vdso.so.1 =>  (0x00007ffdecbdb000)
	libpam.so.0 => /lib/x86_64-linux-gnu/libpam.so.0 (0x00007f3753c5e000)
	libpam_misc.so.0 => /lib/x86_64-linux-gnu/libpam_misc.so.0 (0x00007f3753a5a000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f3753837000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f3753472000)
	libaudit.so.1 => /lib/x86_64-linux-gnu/libaudit.so.1 (0x00007f375324e000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f375304a000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f3752e0c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f3753e6c000)
```

/etc/pam.d 에서 설정 파일들을 확인 가능


## Directory Structure
Directory|Desc
---------|----
/bin|**Common programs**, shared by the system
/sbin|**Programs for use by the system** and the system administrator
/dev|Contains references to all the CPU peripheral hardware
/etc|Most important **system configuration files** are in /etc
/home|Home directories of the common users
/lib|Library files, includes files for all kinds of programs needed by the system and the users
/mnt|Standard mount point for external file systems, e.g. a CD-ROM or a digital camera.
/opt|Typically contains extra and third party software.
/proc|A virtual file system containing information about system resources
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
> df -ih
Filesystem            Inodes   IUsed   IFree IUse% Mounted on
/dev/mapper/vg_bdpapp01-LogVol03
                         22M     29K     22M    1% /
tmpfs                   5.9M       1    5.9M    1% /dev/shm
/dev/sda1                50K      39     50K    1% /boot
/dev/mapper/vg_bdpapp01-LogVol00
                         92M     33K     92M    1% /home
/dev/mapper/vg_bdpapp01-LogVol02
                        3.1M     56K    3.1M    2% /usr
/dev/mapper/vg_bdpapp01-LogVol01
                        313K    3.6K    309K    2% /var
```

### Soft Link
- a soft link map from a directory entry to another directory entry.

### Hard Link
- a hard link is a mapping from a directory entry to an inode

## Booting
- Power ON
- BIOS
	- check hardware
	- select boot device
	- load MBR (Master Boot Record) (booting device first 512kb)
	- load bootloader
- Bootloader
- Kernel Init
- Initd
- login prompt

### upstart (instead of init)

```bash
> initctl list
rc stop/waiting
tty (/dev/tty3) start/running, process 5753
tty (/dev/tty2) start/running, process 5751
tty (/dev/tty1) start/running, process 5749
tty (/dev/tty6) start/running, process 5759
tty (/dev/tty5) start/running, process 5757
tty (/dev/tty4) start/running, process 5755
plymouth-shutdown stop/waiting
control-alt-delete stop/waiting
rcS-emergency stop/waiting
readahead-collector stop/waiting
kexec-disable stop/waiting
quit-plymouth stop/waiting
rcS stop/waiting
prefdm stop/waiting
init-system-dbus stop/waiting
readahead stop/waiting
splash-manager stop/waiting
start-ttys stop/waiting
readahead-disable-services stop/waiting
rcS-sulogin stop/waiting
serial stop/waiting
```

### ubuntu runlevel
runlevel|Desc.|script
--------|-----|------
0|시스템 종료|/etc/rc0.d
1,S,s|단일 사용자 모드|/etc/rc1.d, /etc/rcS.d
2|그래피컬 다중 사용자 모드 + 네트워킹 **(기본)**|/etc/rc2.d
3|2와동일|/etc/rc3.d
4|2와동일|/etc/rc4.d
5|2와동일|/etc/rc5.d
6|시스템 재시작|/etc/rc6.d


### daemon

데몬은 tty를 가지지 않고 background 프로그램은 가진다. 데몬은 보통 PPID 가 1번 즉 initd에 의해서 실행되는 경우가 많다. 슈퍼 데몬의 경우 다른 데몬을 관리하는 데몬을 일컫는다.

#### init daemon
- process 1
```bash
> pstree
init─┬─abrt-dump-oops
     ├─abrtd
     ├─acpid
     ├─atd
     ├─auditd───{auditd}
     ├─automount───4*[{automount}]
     ├─certmonger
     ├─console-kit-dae───63*[{console-kit-da}]
     ├─crond
     ├─cupsd
     ├─dbus-daemon───{dbus-daemon}
     ├─hald───hald-runner─┬─hald-addon-acpi
     │                    └─hald-addon-inpu
     ├─irqbalance
     ├─java───56*[{java}]
     ├─java───35*[{java}]
     ├─java───24*[{java}]
     ├─java───39*[{java}]
     ├─java───68*[{java}]
     ├─java───29*[{java}]
     ├─login───bash
     ├─master───qmgr
     ├─5*[mingetty]
     ├─ntpd
     ├─rpc.idmapd
     ├─rpc.statd
     ├─rpcbind
     ├─rsyslogd───3*[{rsyslogd}]
     ├─sshd───sshd───bash───pstree
     ├─udevd───2*[udevd]
     ├─vsftpd
     └─xinetd
```

### Bootloder
#### GRUB
GRand Unified Bootloader (최신버전 GRUB2)
/boot/grub/grub.cfg
```bash
> cat /boot/grub/grub.conf 
# grub.conf generated by anaconda
#
# Note that you do not have to rerun grub after making changes to this file
# NOTICE:  You have a /boot partition.  This means that
#          all kernel and initrd paths are relative to /boot/, eg.
#          root (hd0,0)
#          kernel /vmlinuz-version ro root=/dev/mapper/vg_bdpapp01-LogVol03
#          initrd /initrd-[generic-]version.img
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.32-279.el6.x86_64)
	root (hd0,0)
	kernel /vmlinuz-2.6.32-279.el6.x86_64 ro root=/dev/mapper/vg_bdpapp01-LogVol03 rd_NO_LUKS  KEYBOARDTYPE=pc KEYTABLE=us LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=vg_bdpapp01/LogVol03 rd_NO_DM rhgb quiet
	initrd /initramfs-2.6.32-279.el6.x86_64.img
```



## LDAP
- LDAP (Lightweight Directory Access Protocol) is an application protocol for querying and modifying items in directory service providers like Active Directory, which supports a form of LDAP.
- 참조와 검색이 많고 갱신이나 삭제가 그다지 빈번하지 않은 사용자 관리에 이용
- 복수 서비스 인증을 통합하는 싱글 사인 온 에도 많이 사용

## Active Directory Service
- Active Directory is a database based system that provides authentication, directory, policy, and other services in a# Network
- Short answer: AD is a directory services database, and LDAP is one of the protocols you can use to talk to it.


## Reference Book
* 우분투 리눅스 시스템 & 네트워크 (4.5/5)
* 리눅스 시스템의 이해와 활용 (4/5)
* Wikipedia
