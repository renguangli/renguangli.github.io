1. 查看硬盘挂载情况

```
[root@localhost /]# lsblk 
NAME            MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda               8:0    0 893.1G  0 disk 
├─sda1            8:1    0   200M  0 part /boot/efi
├─sda2            8:2    0     1G  0 part /boot
└─sda3            8:3    0   892G  0 part 
  ├─centos-root 253:0    0    50G  0 lvm  /
  ├─centos-swap 253:1    0     4G  0 lvm  [SWAP]
  └─centos-home 253:2    0   838G  0 lvm  /home
sdb               8:16   0   4.4T  0 disk

```

硬盘 sdb 还没有挂载

2. 首先格式化硬盘

```
 mkfs.xfs /dev/sdb
```

3. 创建挂载目录，挂载磁盘

```
[root@localhost /]# cd /
[root@localhost /]# mkdir data
[root@localhost /]# mount /dev/sdb /data
```

4. 开机自动挂载

```
[root@localhost /]# vi /etc/fstab 
#
# /etc/fstab
# Created by anaconda on Sat Aug 22 14:50:55 2020
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=c25b474b-c83a-4ab7-a2b1-65cf8328fdd6 /boot                   xfs     defaults        0 0
UUID=04EE-56CA          /boot/efi               vfat    umask=0077,shortname=winnt 0 0
/dev/mapper/centos-home /home                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
/dev/sdb                /data01                  xfs     defaults        0 0
```

5. 执行 `mount` 命令