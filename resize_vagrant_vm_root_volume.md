[root@kubernetes ~]# resize2fs -p -F /dev/sda2
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Device or resource busy while trying to open /dev/sda2
Couldn't find valid filesystem superblock.
[root@kubernetes ~]#
[root@kubernetes ~]#
[root@kubernetes ~]#
[root@kubernetes ~]#
[root@kubernetes ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               9.41 GiB
  PE Size               4.00 MiB
  Total PE              2409
  Alloc PE / Size       2409 / 9.41 GiB
  Free  PE / Size       0 / 0
  VG UUID               NJWfOH-An0K-Hm9Q-8Tjs-yq2x-ZWXi-L2vP7i

[root@kubernetes ~]# parted /dev/sda
GNU Parted 3.1
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) help
  align-check TYPE N                        check partition N for TYPE(min|opt) alignment
  help [COMMAND]                           print general help, or help on COMMAND
  mklabel,mktable LABEL-TYPE               create a new disklabel (partition table)
  mkpart PART-TYPE [FS-TYPE] START END     make a partition
  name NUMBER NAME                         name partition NUMBER as NAME
  print [devices|free|list,all|NUMBER]     display the partition table, available devices, free space, all found partitions, or a particular partition
  quit                                     exit program
  rescue START END                         rescue a lost partition near START and END

  resizepart NUMBER END                    resize partition NUMBER
  rm NUMBER                                delete partition NUMBER
  select DEVICE                            choose the device to edit
  disk_set FLAG STATE                      change the FLAG on selected device
  disk_toggle [FLAG]                       toggle the state of FLAG on selected device
  set NUMBER FLAG STATE                    change the FLAG on partition NUMBER
  toggle [NUMBER [FLAG]]                   toggle the state of FLAG on partition NUMBER
  unit UNIT                                set the default unit to UNIT
  version                                  display the version number and copyright information of GNU Parted
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  525MB   524MB   primary  xfs          boot
 2      525MB   10.6GB  10.1GB  primary               lvm

(parted) resizepart 2 20GB
(parted) p
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sda: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  525MB   524MB   primary  xfs          boot
 2      525MB   20.0GB  19.5GB  primary               lvm

(parted) quit
Information: You may need to update /etc/fstab.

[root@kubernetes ~]# pvresize /dev/sda2
  Physical volume "/dev/sda2" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized
[root@kubernetes ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  6
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               18.13 GiB
  PE Size               4.00 MiB
  Total PE              4642
  Alloc PE / Size       2409 / 9.41 GiB
  Free  PE / Size       2233 / 8.72 GiB
  VG UUID               NJWfOH-An0K-Hm9Q-8Tjs-yq2x-ZWXi-L2vP7i

[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.4G  2.0G  6.5G  24% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]# lvextend  -L+8.72GB /dev/mapper/centos-root
  Rounding size to boundary between physical extents: 8.72 GiB.
  Size of logical volume centos/root changed from <8.42 GiB (2155 extents) to 17.14 GiB (4388 extents).
  Logical volume centos/root successfully resized.
[root@kubernetes ~]# vgdisplay
  --- Volume group ---
  VG Name               centos
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  7
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               18.13 GiB
  PE Size               4.00 MiB
  Total PE              4642
  Alloc PE / Size       4642 / 18.13 GiB
  Free  PE / Size       0 / 0
  VG UUID               NJWfOH-An0K-Hm9Q-8Tjs-yq2x-ZWXi-L2vP7i

[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.4G  2.0G  6.5G  24% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]# resize2fs /dev/mapper/centos-root
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block while trying to open /dev/mapper/centos-root
Couldn't find valid filesystem superblock.
[root@kubernetes ~]# which resize2fs
/sbin/resize2fs
[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.4G  2.0G  6.5G  24% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/centos/swap
  LV Name                swap
  VG Name                centos
  LV UUID                bvMiNE-9iFI-8gT1-gEQt-5OEV-fuj2-2UofR2
  LV Write Access        read/write
  LV Creation host, time localhost, 2015-07-16 08:37:33 +0200
  LV Status              available
  # open                 2
  LV Size                1016.00 MiB
  Current LE             254
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:1

  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                pbWSWT-pkMN-mQqG-zhHd-9MdB-UM05-QVLhfb
  LV Write Access        read/write
  LV Creation host, time localhost, 2015-07-16 08:37:34 +0200
  LV Status              available
  # open                 1
  LV Size                17.14 GiB
  Current LE             4388
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0

[root@kubernetes ~]# lvextend -l +100%FREE /dev/centos/root
  New size (4388 extents) matches existing size (4388 extents).
[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.4G  2.0G  6.5G  24% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]# lvdisplay /dev/centos/root
  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                pbWSWT-pkMN-mQqG-zhHd-9MdB-UM05-QVLhfb
  LV Write Access        read/write
  LV Creation host, time localhost, 2015-07-16 08:37:34 +0200
  LV Status              available
  # open                 1
  LV Size                17.14 GiB
  Current LE             4388
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0

[root@kubernetes ~]# resize2fs /dev/mapper/centos-root
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block while trying to open /dev/mapper/centos-root
Couldn't find valid filesystem superblock.
[root@kubernetes ~]#
[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root  8.4G  2.0G  6.5G  24% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]# grep centos-root

^C
[root@kubernetes ~]#  xfs_growfs /dev/mapper/centos-root
meta-data=/dev/mapper/centos-root isize=256    agcount=4, agsize=549120 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=2196480, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2196480 to 4493312
[root@kubernetes ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.2G     0  1.2G   0% /dev
tmpfs                    1.2G     0  1.2G   0% /dev/shm
tmpfs                    1.2G  8.6M  1.2G   1% /run
tmpfs                    1.2G     0  1.2G   0% /sys/fs/cgroup
/dev/mapper/centos-root   18G  2.0G   16G  12% /
/dev/sda1                497M  150M  348M  31% /boot
/vagrant                 477G   78G  399G  17% /vagrant
tmpfs                    234M     0  234M   0% /run/user/1000
[root@kubernetes ~]#
