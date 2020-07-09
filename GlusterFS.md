
A tool to control WorkloadWisdom.
1. [Installation](#Installation)
2. [prepare bricks](#prepare bricks)
3. [create cluster](#create cluster)
4. [create volume](#create volume)
5. [start volume](#start volume)
5. [Troubleshooting](#Troubleshooting)

# Installation
```bash
# yum -y install centos-release-gluster
# yum -y install glusterfs glusterfs-server glusterfs-fuse

# systemctl start glusterd
# systemctl enable glusterd
# systemctl status glusterd
```

# prepare bricks
```bash
[root@glusterfs_node1 data]# fdisk /dev/sdb 
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0x48f83be3.

Command (m for help): p

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x48f83be3

   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-41943039, default 2048): 
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-41943039, default 41943039): 
Using default value 41943039
Partition 1 of type Linux and of size 20 GiB is set

Command (m for help): p

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x48f83be3

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    41943039    20970496   83  Linux

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   g   create a new empty GPT partition table
   G   create an IRIX (SGI) partition table
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
[root@glusterfs_node1 data]# fdisk -l

Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x48f83be3

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048    41943039    20970496   83  Linux

Disk /dev/sda: 42.9 GB, 42949672960 bytes, 83886080 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x00011337

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048      616447      307200   83  Linux
/dev/sda2          616448     4812799     2098176   82  Linux swap / Solaris
/dev/sda3         4812800    83886079    39536640   83  Linux
[root@glusterfs_node1 data]# mkfs.btrfs /dev/sdb1 
btrfs-progs v3.19.1
See http://btrfs.wiki.kernel.org for more information.

Turning ON incompat feature 'extref': increased hardlink limit per file to 65536
Turning ON incompat feature 'skinny-metadata': reduced-size metadata extent refs
fs created label (null) on /dev/sdb1
	nodesize 16384 leafsize 16384 sectorsize 4096 size 20.00GiB
[root@glusterfs_node1 data]# mount /dev/sdb
sdb   sdb1  
[root@glusterfs_node1 data]# mount /dev/sdb
sdb   sdb1  
[root@glusterfs_node1 data]# mount /dev/sdb1 /data/v1/
[root@glusterfs_node1 data]# echo "/dev/sdb1    /data/v1 btrfs defaults 1 2" >> /etc/fstab
[root@glusterfs_node1 data]# cat /etc/fstab 

#
# /etc/fstab
# Created by anaconda on Tue Jul  7 08:50:00 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=998cc213-e8dc-4af9-ac12-00a6617957d3 /                       xfs     defaults        0 0
UUID=e060d8b9-8d8b-482f-bbf2-869cc7f21d68 /boot                   xfs     defaults        0 0
UUID=fd9bc324-f188-4bc3-ad6e-dd39746fe626 swap                    swap    defaults        0 0
/dev/sdb1    /data/v1 btrfs defaults 1 2
[root@glusterfs_node1 data]# 

```


# create cluster
```bash
[root@glusterfs_node1 network-scripts]# ping glusterfs_node2
PING glusterfs_node2 (192.168.157.130) 56(84) bytes of data.
64 bytes from glusterfs_node2 (192.168.157.130): icmp_seq=1 ttl=64 time=0.620 ms
64 bytes from glusterfs_node2 (192.168.157.130): icmp_seq=2 ttl=64 time=1.13 ms
64 bytes from glusterfs_node2 (192.168.157.130): icmp_seq=3 ttl=64 time=0.911 ms
^C
--- glusterfs_node2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.620/0.888/1.134/0.211 ms
[root@glusterfs_node1 network-scripts]# gluster peer probe 192.168.157.130
peer probe: success. 
[root@glusterfs_node1 network-scripts]# gluster peer status
Number of Peers: 1

Hostname: 192.168.157.130
Uuid: 8d8daaec-de9f-4d77-a7b7-4b7d39dac3e4
State: Accepted peer request (Connected)

[root@glusterfs_node1 data]# gluster peer probe glusterfs_node2
glusterfs_node2 is an invalid address

Usage:
peer probe { <HOSTNAME> | <IP-address> }

[root@glusterfs_node1 data]# hostnamectl 
   Static hostname: glusterfs_node1
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 9a6c9ca2d7aa4739ac42c4781eb90434
           Boot ID: 83c6c5d0fde24f88b3eeaae2e5476d8f
    Virtualization: vmware
  Operating System: CentOS Linux 7 (Core)
       CPE OS Name: cpe:/o:centos:centos:7
            Kernel: Linux 3.10.0-327.28.3.el7.x86_64
      Architecture: x86-64
[root@glusterfs_node1 data]# hostnamectl set-hostname glusterfs-node1
[root@glusterfs_node1 data]# gluster peer probe glusterfs-node2
peer probe: success. 
[root@glusterfs_node1 data]# gluster pool list
UUID					Hostname       	State
8d8daaec-de9f-4d77-a7b7-4b7d39dac3e4	glusterfs-node2	Connected 
888a0650-c8a8-4c50-af7e-cbcbbb56d575	localhost      	Connected 
```

# create volume
```bash
[root@glusterfs-node1 brick]# ll
total 0
[root@glusterfs-node1 brick]# pwd
/data/v1/brick
[root@glusterfs-node1 ~]# gluster volume create hello_v1 replica 3 glusterfs-node1:/data/v1/brick glusterfs-node2:/data/v1/brick glusterfs-node3:/data/v1/brick
volume create: hello_v1: success: please start the volume to access data
```
# start volume
```bash
[root@glusterfs-node1 ~]# gluster volume status
Volume hello_v1 is not started
 
[root@glusterfs-node1 ~]# gluster volume start hello_v1
volume start: hello_v1: success
[root@glusterfs-node1 ~]# gluster volume status
Status of volume: hello_v1
Gluster process                             TCP Port  RDMA Port  Online  Pid
------------------------------------------------------------------------------
Brick glusterfs-node1:/data/v1/brick        49152     0          Y       15608
Brick glusterfs-node2:/data/v1/brick        49152     0          Y       14965
Brick glusterfs-node3:/data/v1/brick        49152     0          Y       48392
Self-heal Daemon on localhost               N/A       N/A        Y       15629
Self-heal Daemon on glusterfs-node3         N/A       N/A        Y       48413
Self-heal Daemon on glusterfs-node2         N/A       N/A        Y       14986
 
Task Status of Volume hello_v1
------------------------------------------------------------------------------
There are no active volume tasks
[root@glusterfs-node1 ~]# gluster volume info hello_v1 
 
Volume Name: hello_v1
Type: Replicate
Volume ID: c98737ac-7e48-4184-a193-c77557c07e32
Status: Started
Snapshot Count: 0
Number of Bricks: 1 x 3 = 3
Transport-type: tcp
Bricks:
Brick1: glusterfs-node1:/data/v1/brick
Brick2: glusterfs-node2:/data/v1/brick
Brick3: glusterfs-node3:/data/v1/brick
Options Reconfigured:
transport.address-family: inet
storage.fips-mode-rchecksum: on
nfs.disable: on
performance.client-io-threads: off
```

# mount volume
```bash
☁  /mnt  apt-get install glusterfs-client -y
☁  /mnt  mount -t glusterfs glusterfs-node1:/hello_v1 ./  

glusterfs-node1:/hello_v1 on /mnt type fuse.glusterfs (rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,max_read=131072)
```

# heal a volume
```bash
[root@glusterfs-node1 brick]# pwd
/data/v1/brick
[root@glusterfs-node1 brick]# rm -rf *
[root@glusterfs-node1 brick]# ll
total 0

[root@glusterfs-node1 brick]# gluster volume heal hello_v1 full
Launching heal operation to perform full self heal on volume hello_v1 has been successful 
Use heal info commands to check status.
[root@glusterfs-node1 brick]# gluster volume heal hello_v1 info summary
Brick glusterfs-node1:/data/v1/brick
Status: Transport endpoint is not connected
Total Number of entries: -
Number of entries in heal pending: -
Number of entries in split-brain: -
Number of entries possibly healing: -

Brick glusterfs-node2:/data/v1/brick
Status: Connected
Total Number of entries: 4
Number of entries in heal pending: 4
Number of entries in split-brain: 0
Number of entries possibly healing: 0

Brick glusterfs-node3:/data/v1/brick
Status: Connected
Total Number of entries: 4
Number of entries in heal pending: 4
Number of entries in split-brain: 0
Number of entries possibly healing: 0

[root@glusterfs-node1 brick]# ll
total 232652
...
drwxr-xr-x. 1 root root    268374 Jul  8 23:05 gfs
...
[root@glusterfs-node1 v1]# du -sk brick/
18840100	brick/
[root@glusterfs-node2 v1]# du -sk brick/
18840100	brick/

```


# Troubleshooting

Failed to start GlusterFS, a clustered file-system server.
```bash
[root@localhost gfs]# systemctl start glusterd
Job for glusterd.service failed because the control process exited with error code. See "systemctl status glusterd.service" and "journalctl -xe" for details.
[root@localhost gfs]# systemctl status glusterd.service
● glusterd.service - GlusterFS, a clustered file-system server
   Loaded: loaded (/usr/lib/systemd/system/glusterd.service; enabled; vendor preset: enabled)
   Active: failed (Result: exit-code) since Tue 2020-07-07 00:37:49 PDT; 10s ago
     Docs: man:glusterd(8)
  Process: 14380 ExecStart=/usr/sbin/glusterd -p /var/run/glusterd.pid --log-level $LOG_LEVEL $GLUSTERD_OPTIONS (code=exited, status=1/FAILURE)

Jul 07 00:37:49 localhost.localdomain systemd[1]: Starting GlusterFS, a clustered file-system server...
Jul 07 00:37:49 localhost.localdomain systemd[1]: glusterd.service: control process exited, code=exited status=1
Jul 07 00:37:49 localhost.localdomain systemd[1]: Failed to start GlusterFS, a clustered file-system server.
Jul 07 00:37:49 localhost.localdomain systemd[1]: Unit glusterd.service entered failed state.
Jul 07 00:37:49 localhost.localdomain systemd[1]: glusterd.service failed.
[root@localhost gfs]# man glusterd
[root@localhost gfs]# tail /var/log/glusterfs/glusterd.log
[2020-07-07 07:37:49.865602] I [MSGID: 106479] [glusterd.c:1482:init] 0-management: Using /var/lib/glusterd as working directory 
[2020-07-07 07:37:49.865606] I [MSGID: 106479] [glusterd.c:1488:init] 0-management: Using /var/run/gluster as pid file working directory 
[2020-07-07 07:37:49.866969] E [rpc-transport.c:300:rpc_transport_load] 0-rpc-transport: /usr/lib64/glusterfs/7.6/rpc-transport/socket.so: symbol SSL_CTX_get0_param, version libssl.so.10 not defined in file libssl.so.10 with link time reference
[2020-07-07 07:37:49.866977] W [rpc-transport.c:304:rpc_transport_load] 0-rpc-transport: volume 'socket.management': transport-type 'socket' is not valid or not found on this machine
[2020-07-07 07:37:49.866981] W [rpcsvc.c:1981:rpcsvc_create_listener] 0-rpc-service: cannot create listener, initing the transport failed
[2020-07-07 07:37:49.866984] E [MSGID: 106244] [glusterd.c:1774:init] 0-management: creation of listener failed 
[2020-07-07 07:37:49.866989] E [MSGID: 101019] [xlator.c:629:xlator_init] 0-management: Initialization of volume 'management' failed, review your volfile again 
[2020-07-07 07:37:49.866993] E [MSGID: 101066] [graph.c:425:glusterfs_graph_init] 0-management: initializing translator failed 
[2020-07-07 07:37:49.866997] E [MSGID: 101176] [graph.c:779:glusterfs_graph_activate] 0-graph: init failed 
[2020-07-07 07:37:49.867082] W [glusterfsd.c:1596:cleanup_and_exit] (-->/usr/sbin/glusterd(glusterfs_volumes_init+0xfd) [0x7fb40583a19d] -->/usr/sbin/glusterd(glusterfs_process_volfp+0x21d) [0x7fb40583a08d] -->/usr/sbin/glusterd(cleanup_and_exit+0x6b) [0x7fb40583948b] ) 0-: received signum (-1), shutting down 
```
_Solution_
```bash
[root@localhost gfs]# yum update openssl
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: repos.mia.quadranet.com
 * centos-gluster7: repos-tx.psychz.net
 * extras: mirror.netdepot.com
 * updates: us.mirror.nsec.pt
Resolving Dependencies
--> Running transaction check
---> Package openssl.x86_64 1:1.0.1e-51.el7_2.5 will be updated
---> Package openssl.x86_64 1:1.0.2k-19.el7 will be an update
--> Processing Dependency: openssl-libs(x86-64) = 1:1.0.2k-19.el7 for package: 1:openssl-1.0.2k-19.el7.x86_64
--> Processing Dependency: libcrypto.so.10(OPENSSL_1.0.2)(64bit) for package: 1:openssl-1.0.2k-19.el7.x86_64
--> Running transaction check
---> Package openssl-libs.x86_64 1:1.0.1e-51.el7_2.5 will be updated
---> Package openssl-libs.x86_64 1:1.0.2k-19.el7 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================================================================================================================================
 Package                                              Arch                                           Version                                                    Repository                                    Size
===================================================================================================================================================================================================================
Updating:
 openssl                                              x86_64                                         1:1.0.2k-19.el7                                            base                                         493 k
Updating for dependencies:
 openssl-libs                                         x86_64                                         1:1.0.2k-19.el7                                            base                                         1.2 M

Transaction Summary
===================================================================================================================================================================================================================
Upgrade  1 Package (+1 Dependent package)

Total download size: 1.7 M
Is this ok [y/d/N]: y
Downloading packages:
No Presto metadata available for base
(1/2): openssl-libs-1.0.2k-19.el7.x86_64.rpm                                                                                                                                                | 1.2 MB  00:00:02     
(2/2): openssl-1.0.2k-19.el7.x86_64.rpm                                                                                                                                                     | 493 kB  00:00:03     
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                                                              505 kB/s | 1.7 MB  00:00:03     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 1:openssl-libs-1.0.2k-19.el7.x86_64                                                                                                                                                             1/4 
  Updating   : 1:openssl-1.0.2k-19.el7.x86_64                                                                                                                                                                  2/4 
  Cleanup    : 1:openssl-1.0.1e-51.el7_2.5.x86_64                                                                                                                                                              3/4 
  Cleanup    : 1:openssl-libs-1.0.1e-51.el7_2.5.x86_64                                                                                                                                                         4/4 
  Verifying  : 1:openssl-libs-1.0.2k-19.el7.x86_64                                                                                                                                                             1/4 
  Verifying  : 1:openssl-1.0.2k-19.el7.x86_64                                                                                                                                                                  2/4 
  Verifying  : 1:openssl-libs-1.0.1e-51.el7_2.5.x86_64                                                                                                                                                         3/4 
  Verifying  : 1:openssl-1.0.1e-51.el7_2.5.x86_64                                                                                                                                                              4/4 

Updated:
  openssl.x86_64 1:1.0.2k-19.el7                                                                                                                                                                                   

Dependency Updated:
  openssl-libs.x86_64 1:1.0.2k-19.el7                                                                                                                                                                              

Complete!

[root@localhost gfs]# systemctl start glusterd
[root@localhost gfs]# systemctl status glusterd
● glusterd.service - GlusterFS, a clustered file-system server
   Loaded: loaded (/usr/lib/systemd/system/glusterd.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-07-07 00:42:22 PDT; 20s ago
     Docs: man:glusterd(8)
  Process: 14706 ExecStart=/usr/sbin/glusterd -p /var/run/glusterd.pid --log-level $LOG_LEVEL $GLUSTERD_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 14707 (glusterd)
   CGroup: /system.slice/glusterd.service
           └─14707 /usr/sbin/glusterd -p /var/run/glusterd.pid --log-level INFO

Jul 07 00:42:22 localhost.localdomain systemd[1]: Starting GlusterFS, a clustered file-system server...
Jul 07 00:42:22 localhost.localdomain systemd[1]: Started GlusterFS, a clustered file-system server.
```

glusterfs_node2 is an invalid address
```bash
[root@glusterfs_node1 data]# gluster peer probe glusterfs_node2
glusterfs_node2 is an invalid address

Usage:
peer probe { <HOSTNAME> | <IP-address> }
```

_solution_
```bash
# underscore is not allowed in hostname
# https://stackoverflow.com/questions/2180465/can-domain-name-subdomains-have-an-underscore-in-it
hostnamectl set-hostname glusterfs-node1
```

gluster peer status returns 'Disconnected'
```bash
[root@glusterfs-node2 Desktop]# gluster peer status
Number of Peers: 1

Hostname: glusterfs-node1
Uuid: 888a0650-c8a8-4c50-af7e-cbcbbb56d575
State: Peer in Cluster (Disconnected)
[root@glusterfs-node2 Desktop]# gluster pool list
UUID					Hostname       	State
888a0650-c8a8-4c50-af7e-cbcbbb56d575	glusterfs-node1	Disconnected 
8d8daaec-de9f-4d77-a7b7-4b7d39dac3e4	localhost      	Connected 
```

_solution_

firewalld issue 
```bash
[root@glusterfs-node1 Desktop]# systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2020-07-07 22:24:55 PDT; 4min 30s ago
 Main PID: 857 (firewalld)
   CGroup: /system.slice/firewalld.service
           └─857 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

Jul 08 07:24:54 glusterfs-node1 systemd[1]: Starting firewalld - dynamic firewall daemon...
Jul 07 22:24:55 glusterfs-node1 systemd[1]: Started firewalld - dynamic firewall daemon.
[root@glusterfs-node1 Desktop]# systemctl stop firewalld
[root@glusterfs-node1 Desktop]# systemctl disable firewalld
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.
[root@glusterfs-node1 Desktop]# 

[root@glusterfs-node2 Desktop]# gluster pool list
UUID					Hostname       	State
888a0650-c8a8-4c50-af7e-cbcbbb56d575	glusterfs-node1	Connected 
8d8daaec-de9f-4d77-a7b7-4b7d39dac3e4	localhost      	Connected 
[root@glusterfs-node2 Desktop]# gluster peer status
Number of Peers: 1

Hostname: glusterfs-node1
Uuid: 888a0650-c8a8-4c50-af7e-cbcbbb56d575
State: Peer in Cluster (Connected)
[root@glusterfs-node2 Desktop]# 
```

* brick is a mount point
```bash
[root@glusterfs-node1 v1]# gluster volume create hello_v1 replica 3 glusterfs-node1:/data/v1 glusterfs-node2:/data/v1 glusterfs-node3:/data/v1
volume create: hello_v1: failed: The brick glusterfs-node1:/data/v1 is a mount point. Please create a sub-directory under the mount point and use that as the brick directory. Or use 'force' at the end of the command if you want to override this behavior.
```

_solution_
```bash
[root@glusterfs-node1 ~]# gluster volume create hello_v1 replica 3 glusterfs-node1:/data/v1/brick glusterfs-node2:/data/v1/brick glusterfs-node3:/data/v1/brick
volume create: hello_v1: success: please start the volume to access data
```

* Brick ops failed on localhost
```bash
[root@glusterfs-node1 ~]# gluster volume profile hello_v1 info nfs
Profile on Volume hello_v1 is not started
[root@glusterfs-node1 ~]# gluster volume profile hello_v1 start nfs
Starting volume profile on hello_v1 has been successful 
[root@glusterfs-node1 ~]# gluster volume profile hello_v1 info nfs
Brick ops failed on localhost. Please check log file for details
```
_solution_

* 
```bash
[root@glusterfs-node1 ~]# gluster volume get hello_v1 all
Option                                  Value                                   
------                                  -----                                   
cluster.lookup-unhashed                 on                                      
cluster.lookup-optimize                 on                                      
cluster.min-free-disk                   10%                                     
cluster.min-free-inodes                 5%                                      
cluster.rebalance-stats                 off                                     
cluster.subvols-per-directory           (null)                                  
cluster.readdir-optimize                off                                     
cluster.rsync-hash-regex                (null)                                  
cluster.extra-hash-regex                (null)                                  
cluster.dht-xattr-name                  trusted.glusterfs.dht                   
cluster.randomize-hash-range-by-gfid    off                                     
cluster.rebal-throttle                  normal                                  
cluster.lock-migration                  off                                     
cluster.force-migration                 off                                     
cluster.local-volume-name               (null)                                  
cluster.weighted-rebalance              on                                      
cluster.switch-pattern                  (null)                                  
cluster.entry-change-log                on                                      
cluster.read-subvolume                  (null)                                  
cluster.read-subvolume-index            -1                                      
cluster.read-hash-mode                  1                                       
cluster.background-self-heal-count      8                                       
cluster.metadata-self-heal              off                                     
cluster.data-self-heal                  off                                     
cluster.entry-self-heal                 off                                     
cluster.self-heal-daemon                on                                      
cluster.heal-timeout                    600                                     
cluster.self-heal-window-size           1                                       
cluster.data-change-log                 on                                      
cluster.metadata-change-log             on                                      
cluster.data-self-heal-algorithm        (null)                                  
cluster.eager-lock                      on                                      
disperse.eager-lock                     on                                      
disperse.other-eager-lock               on                                      
disperse.eager-lock-timeout             1                                       
disperse.other-eager-lock-timeout       1                                       
cluster.quorum-type                     auto                                    
cluster.quorum-count                    (null)                                  
cluster.choose-local                    true                                    
cluster.self-heal-readdir-size          1KB                                     
cluster.post-op-delay-secs              1                                       
cluster.ensure-durability               on                                      
cluster.consistent-metadata             no                                      
cluster.heal-wait-queue-length          128                                     
cluster.favorite-child-policy           none                                    
cluster.full-lock                       yes                                     
cluster.optimistic-change-log           on                                      
diagnostics.latency-measurement         on                                      
diagnostics.dump-fd-stats               off                                     
diagnostics.count-fop-hits              on                                      
diagnostics.brick-log-level             INFO                                    
diagnostics.client-log-level            INFO                                    
diagnostics.brick-sys-log-level         CRITICAL                                
diagnostics.client-sys-log-level        CRITICAL                                
diagnostics.brick-logger                (null)                                  
diagnostics.client-logger               (null)                                  
diagnostics.brick-log-format            (null)                                  
diagnostics.client-log-format           (null)                                  
diagnostics.brick-log-buf-size          5                                       
diagnostics.client-log-buf-size         5                                       
diagnostics.brick-log-flush-timeout     120                                     
diagnostics.client-log-flush-timeout    120                                     
diagnostics.stats-dump-interval         0                                       
diagnostics.fop-sample-interval         0                                       
diagnostics.stats-dump-format           json                                    
diagnostics.fop-sample-buf-size         65535                                   
diagnostics.stats-dnscache-ttl-sec      86400                                   
performance.cache-max-file-size         0                                       
performance.cache-min-file-size         0                                       
performance.cache-refresh-timeout       1                                       
performance.cache-priority                                                      
performance.cache-size                  32MB                                    
performance.io-thread-count             16                                      
performance.high-prio-threads           16                                      
performance.normal-prio-threads         16                                      
performance.low-prio-threads            16                                      
performance.least-prio-threads          1                                       
performance.enable-least-priority       on                                      
performance.iot-watchdog-secs           (null)                                  
performance.iot-cleanup-disconnected-reqsoff                                     
performance.iot-pass-through            false                                   
performance.io-cache-pass-through       false                                   
performance.cache-size                  128MB                                   
performance.qr-cache-timeout            1                                       
performance.cache-invalidation          false                                   
performance.ctime-invalidation          false                                   
performance.flush-behind                on                                      
performance.nfs.flush-behind            on                                      
performance.write-behind-window-size    1MB                                     
performance.resync-failed-syncs-after-fsyncoff                                     
performance.nfs.write-behind-window-size1MB                                     
performance.strict-o-direct             off                                     
performance.nfs.strict-o-direct         off                                     
performance.strict-write-ordering       off                                     
performance.nfs.strict-write-ordering   off                                     
performance.write-behind-trickling-writeson                                      
performance.aggregate-size              128KB                                   
performance.nfs.write-behind-trickling-writeson                                      
performance.lazy-open                   yes                                     
performance.read-after-open             yes                                     
performance.open-behind-pass-through    false                                   
performance.read-ahead-page-count       4                                       
performance.read-ahead-pass-through     false                                   
performance.readdir-ahead-pass-through  false                                   
performance.md-cache-pass-through       false                                   
performance.md-cache-timeout            1                                       
performance.cache-swift-metadata        true                                    
performance.cache-samba-metadata        false                                   
performance.cache-capability-xattrs     true                                    
performance.cache-ima-xattrs            true                                    
performance.md-cache-statfs             off                                     
performance.xattr-cache-list                                                    
performance.nl-cache-pass-through       false                                   
network.frame-timeout                   1800                                    
network.ping-timeout                    42                                      
network.tcp-window-size                 (null)                                  
client.ssl                              off                                     
network.remote-dio                      disable                                 
client.event-threads                    2                                       
client.tcp-user-timeout                 0                                       
client.keepalive-time                   20                                      
client.keepalive-interval               2                                       
client.keepalive-count                  9                                       
network.tcp-window-size                 (null)                                  
network.inode-lru-limit                 16384                                   
auth.allow                              *                                       
auth.reject                             (null)                                  
transport.keepalive                     1                                       
server.allow-insecure                   on                                      
server.root-squash                      off                                     
server.all-squash                       off                                     
server.anonuid                          65534                                   
server.anongid                          65534                                   
server.statedump-path                   /var/run/gluster                        
server.outstanding-rpc-limit            64                                      
server.ssl                              off                                     
auth.ssl-allow                          *                                       
server.manage-gids                      off                                     
server.dynamic-auth                     on                                      
client.send-gids                        on                                      
server.gid-timeout                      300                                     
server.own-thread                       (null)                                  
server.event-threads                    2                                       
server.tcp-user-timeout                 42                                      
server.keepalive-time                   20                                      
server.keepalive-interval               2                                       
server.keepalive-count                  9                                       
transport.listen-backlog                1024                                    
transport.address-family                inet                                    
performance.write-behind                on                                      
performance.read-ahead                  on                                      
performance.readdir-ahead               on                                      
performance.io-cache                    on                                      
performance.open-behind                 on                                      
performance.quick-read                  on                                      
performance.nl-cache                    off                                     
performance.stat-prefetch               on                                      
performance.client-io-threads           off                                     
performance.nfs.write-behind            on                                      
performance.nfs.read-ahead              off                                     
performance.nfs.io-cache                off                                     
performance.nfs.quick-read              off                                     
performance.nfs.stat-prefetch           off                                     
performance.nfs.io-threads              off                                     
performance.force-readdirp              true                                    
performance.cache-invalidation          false                                   
performance.global-cache-invalidation   true                                    
features.uss                            off                                     
features.snapshot-directory             .snaps                                  
features.show-snapshot-directory        off                                     
features.tag-namespaces                 off                                     
network.compression                     off                                     
network.compression.window-size         -15                                     
network.compression.mem-level           8                                       
network.compression.min-size            0                                       
network.compression.compression-level   -1                                      
network.compression.debug               false                                   
features.default-soft-limit             80%                                     
features.soft-timeout                   60                                      
features.hard-timeout                   5                                       
features.alert-time                     86400                                   
features.quota-deem-statfs              off                                     
geo-replication.indexing                off                                     
geo-replication.indexing                off                                     
geo-replication.ignore-pid-check        off                                     
geo-replication.ignore-pid-check        off                                     
features.quota                          off                                     
features.inode-quota                    off                                     
features.bitrot                         disable                                 
debug.trace                             off                                     
debug.log-history                       no                                      
debug.log-file                          no                                      
debug.exclude-ops                       (null)                                  
debug.include-ops                       (null)                                  
debug.error-gen                         off                                     
debug.error-failure                     (null)                                  
debug.error-number                      (null)                                  
debug.random-failure                    off                                     
debug.error-fops                        (null)                                  
nfs.disable                             on                                      
features.read-only                      off                                     
features.worm                           off                                     
features.worm-file-level                off                                     
features.worm-files-deletable           on                                      
features.default-retention-period       120                                     
features.retention-mode                 relax                                   
features.auto-commit-period             180                                     
storage.linux-aio                       off                                     
storage.batch-fsync-mode                reverse-fsync                           
storage.batch-fsync-delay-usec          0                                       
storage.owner-uid                       -1                                      
storage.owner-gid                       -1                                      
storage.node-uuid-pathinfo              off                                     
storage.health-check-interval           30                                      
storage.build-pgfid                     off                                     
storage.gfid2path                       on                                      
storage.gfid2path-separator             :                                       
storage.reserve                         1                                       
storage.reserve-size                    0                                       
storage.health-check-timeout            10                                      
storage.fips-mode-rchecksum             on                                      
storage.force-create-mode               0000                                    
storage.force-directory-mode            0000                                    
storage.create-mask                     0777                                    
storage.create-directory-mask           0777                                    
storage.max-hardlinks                   100                                     
features.ctime                          on                                      
config.gfproxyd                         off                                     
cluster.server-quorum-type              off                                     
cluster.server-quorum-ratio             51                                      
changelog.changelog                     off                                     
changelog.changelog-dir                 {{ brick.path }}/.glusterfs/changelogs  
changelog.encoding                      ascii                                   
changelog.rollover-time                 15                                      
changelog.fsync-interval                5                                       
changelog.changelog-barrier-timeout     120                                     
changelog.capture-del-path              off                                     
features.barrier                        disable                                 
features.barrier-timeout                120                                     
features.trash                          off                                     
features.trash-dir                      .trashcan                               
features.trash-eliminate-path           (null)                                  
features.trash-max-filesize             5MB                                     
features.trash-internal-op              off                                     
cluster.enable-shared-storage           disable                                 
locks.trace                             off                                     
locks.mandatory-locking                 off                                     
cluster.disperse-self-heal-daemon       enable                                  
cluster.quorum-reads                    no                                      
client.bind-insecure                    (null)                                  
features.shard                          off                                     
features.shard-block-size               64MB                                    
features.shard-lru-limit                16384                                   
features.shard-deletion-rate            100                                     
features.scrub-throttle                 lazy                                    
features.scrub-freq                     biweekly                                
features.scrub                          false                                   
features.expiry-time                    120                                     
features.cache-invalidation             off                                     
features.cache-invalidation-timeout     60                                      
features.leases                         off                                     
features.lease-lock-recall-timeout      60                                      
disperse.background-heals               8                                       
disperse.heal-wait-qlength              128                                     
cluster.heal-timeout                    600                                     
dht.force-readdirp                      on                                      
disperse.read-policy                    gfid-hash                               
cluster.shd-max-threads                 1                                       
cluster.shd-wait-qlength                1024                                    
cluster.locking-scheme                  full                                    
cluster.granular-entry-heal             no                                      
features.locks-revocation-secs          0                                       
features.locks-revocation-clear-all     false                                   
features.locks-revocation-max-blocked   0                                       
features.locks-monkey-unlocking         false                                   
features.locks-notify-contention        no                                      
features.locks-notify-contention-delay  5                                       
disperse.shd-max-threads                1                                       
disperse.shd-wait-qlength               1024                                    
disperse.cpu-extensions                 auto                                    
disperse.self-heal-window-size          1                                       
cluster.use-compound-fops               off                                     
performance.parallel-readdir            off                                     
performance.rda-request-size            131072                                  
performance.rda-low-wmark               4096                                    
performance.rda-high-wmark              128KB                                   
performance.rda-cache-limit             10MB                                    
performance.nl-cache-positive-entry     false                                   
performance.nl-cache-limit              10MB                                    
performance.nl-cache-timeout            60                                      
cluster.brick-multiplex                 disable                                 
glusterd.vol_count_per_thread           100                                     
cluster.max-bricks-per-process          250                                     
disperse.optimistic-change-log          on                                      
disperse.stripe-cache                   4                                       
cluster.halo-enabled                    False                                   
cluster.halo-shd-max-latency            99999                                   
cluster.halo-nfsd-max-latency           5                                       
cluster.halo-max-latency                5                                       
cluster.halo-max-replicas               99999                                   
cluster.halo-min-replicas               2                                       
features.selinux                        on                                      
cluster.daemon-log-level                INFO                                    
debug.delay-gen                         off                                     
delay-gen.delay-percentage              10%                                     
delay-gen.delay-duration                100000                                  
delay-gen.enable                                                                
disperse.parallel-writes                on                                      
features.sdfs                           off                                     
features.cloudsync                      off                                     
features.ctime                          on                                      
ctime.noatime                           on                                      
features.cloudsync-storetype            (null)                                  
features.enforce-mandatory-lock         off                                     
config.global-threading                 off                                     
config.client-threads                   16                                      
config.brick-threads                    16                                      
features.cloudsync-remote-read          off                                     
features.cloudsync-store-id             (null)                                  
features.cloudsync-product-id           (null) 
```

* enable nfs
```bash
[root@glusterfs-node1 ~]# gluster volume set hello_v1 nfs.disable off
Gluster NFS is being deprecated in favor of NFS-Ganesha Enter "yes" to continue using Gluster NFS (y/n) y
volume set: success
```


* Refernece: https://www.cnblogs.com/saneri/p/12378121.html
