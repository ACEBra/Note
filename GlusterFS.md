# Installation

```bash
# yum -y install centos-release-gluster
# yum -y install glusterfs glusterfs-server glusterfs-fuse

# systemctl start glusterd
# systemctl enable glusterd
# systemctl status glusterd
```

## Troubleshooting

* Failed to start GlusterFS, a clustered file-system server.
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
* Solution
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

