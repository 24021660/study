```shell
[root@siger-master opt]# df -h
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root   38G   29G  9.0G   76% /
devtmpfs                 5.8G     0  5.8G    0% /dev
tmpfs                    5.8G  4.0K  5.8G    1% /dev/shm
tmpfs                    5.8G   25M  5.8G    1% /run
tmpfs                    5.8G     0  5.8G    0% /sys/fs/cgroup
/dev/xvda1              1014M  142M  873M   14% /boot
/dev/mapper/centos-home   19G  100M   18G    1% /home
tmpfs                    1.2G     0  1.2G    0% /run/user/0
[root@siger-master opt]# vgextend centos /dev/xvdb
  Volume group "centos" successfully extended
[root@siger-master opt]# lvextend -L +500G /dev/mapper/centos-root
  Size of logical volume centos/root changed from <37.04 GiB (9481 extents) to <537.04 GiB (137481 extents).
  Logical volume centos/root successfully resized.
[root@siger-master opt]# xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=512    agcount=4, agsize=2427136 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=9708544, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=4740, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 9708544 to 140780544
[root@siger-master opt]# df -h
文件系统                 容量  已用  可用 已用% 挂载点
/dev/mapper/centos-root  538G   29G  509G    6% /
devtmpfs                 5.8G     0  5.8G    0% /dev
tmpfs                    5.8G  4.0K  5.8G    1% /dev/shm
tmpfs                    5.8G   25M  5.8G    1% /run
tmpfs                    5.8G     0  5.8G    0% /sys/fs/cgroup
/dev/xvda1              1014M  142M  873M   14% /boot
/dev/mapper/centos-home   19G  100M   18G    1% /home
tmpfs                    1.2G     0  1.2G    0% /run/user/0
```