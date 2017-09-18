---
title: Oracle VM - resize root disk
date: 2017-08-17 14:27:34
tags:
- Oracle VM
- Linux
---
1. Check current virtual disk
{% codeblock line_number:false lang:bash %}
[root@localhost ~]# fdisk -l /dev/xvda

Disk /dev/xvda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b3dc8

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048     1026047      512000   83  Linux
/dev/xvda2         1026048    41943039    20458496   8e  Linux LVM
{% endcodeblock %}
<!-- more -->
2. Edit virtual disk in Oracle VM Manager, e.g. adjust the size from 20G to 80G
{% img "/images/2017-08-17 14_29_11-Oracle VM Home.png"  %}
2. Reboot the VM
{% codeblock line_number:false lang:bash %}
[root@localhost ~]# fdisk -l /dev/xvda

Disk /dev/xvda: 85.9 GB, 85899345920 bytes, 167772160 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b3dc8

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048     1026047      512000   83  Linux
/dev/xvda2         1026048    41943039    20458496   8e  Linux LVM
{% endcodeblock %}
The Disk size has been changed from 21.5 GB to 85.9 GB
4. Delete and recreate the LVM partition (**the data will be remain unchanged**)
{% codeblock line_number:false lang:bash %}
[root@hktasu168 ~]# fdisk /dev/xvda
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


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

Command (m for help): p

Disk /dev/xvda: 85.9 GB, 85899345920 bytes, 167772160 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b3dc8

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048     1026047      512000   83  Linux
/dev/xvda2         1026048    41943039    20458496   8e  Linux LVM

Command (m for help): d
Partition number (1,2, default 2): 2
Partition 2 is deleted

Command (m for help): n
Partition type:
   p   primary (1 primary, 0 extended, 3 free)
   e   extended
Select (default p): p
Partition number (2-4, default 2): 2
First sector (1026048-167772159, default 1026048):
Using default value 1026048
Last sector, +sectors or +size{K,M,G} (1026048-167772159, default 167772159):
Using default value 167772159
Partition 2 of type Linux and of size 79.5 GiB is set

Command (m for help): p

Disk /dev/xvda: 85.9 GB, 85899345920 bytes, 167772160 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000b3dc8

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *        2048     1026047      512000   83  Linux
/dev/xvda2         1026048   167772159    83373056   83  Linux

Command (m for help): t
Partition number (1,2, default 2): 2
Hex code (type L to list all codes): L

 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT
1e  Hidden W95 FAT1 80  Old Minix
Hex code (type L to list all codes): 8e
Changed type of partition 'Linux' to 'Linux LVM'

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
{% endcodeblock %}
5. Reboot the VM
6. Resize the physical volume
{% codeblock line_number:false lang:bash %}
[root@localhost ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/xvda2
  VG Name               ol
  PV Size               19.51 GiB / not usable 2.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              4994
  Free PE               0
  Allocated PE          4994
  PV UUID               U3oe3C-7qVe-YuHm-5toc-QfDF-YYWn-6hw6vB

[root@localhost ~]# pvresize /dev/xvda2
  Physical volume "/dev/xvda2" changed
  1 physical volume(s) resized / 0 physical volume(s) not resized
[root@localhost ~]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/xvda2
  VG Name               ol
  PV Size               79.51 GiB / not usable 2.00 MiB
  Allocatable           yes
  PE Size               4.00 MiB
  Total PE              20354
  Free PE               15360
  Allocated PE          4994
  PV UUID               U3oe3C-7qVe-YuHm-5toc-QfDF-YYWn-6hw6vB
{% endcodeblock %}
7. Resize the root volume
{% codeblock line_number:false lang:bash %}
[root@hktasu168 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/ol/swap
  LV Name                swap
  VG Name                ol
  LV UUID                Zv0fXc-mgql-6XAZ-fq0o-Ek3j-YGwz-nsSsUY
  LV Write Access        read/write
  LV Creation host, time localhost, 2017-07-06 03:13:47 -0400
  LV Status              available
  # open                 2
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0

  --- Logical volume ---
  LV Path                /dev/ol/root
  LV Name                root
  VG Name                ol
  LV UUID                1xdvMc-tkPn-CsIM-vxwC-3uv8-fNZx-y0h1mf
  LV Write Access        read/write
  LV Creation host, time localhost, 2017-07-06 03:13:47 -0400
  LV Status              available
  # open                 1
  LV Size                17.51 GiB
  Current LE             4482
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1

[root@hktasu168 ~]# lvextend -l +100%FREE  /dev/ol/root
  Size of logical volume ol/root changed from 17.51 GiB (4482 extents) to 77.51 GiB (19842 extents).
  Logical volume ol/root successfully resized.
[root@hktasu168 ~]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/ol/swap
  LV Name                swap
  VG Name                ol
  LV UUID                Zv0fXc-mgql-6XAZ-fq0o-Ek3j-YGwz-nsSsUY
  LV Write Access        read/write
  LV Creation host, time localhost, 2017-07-06 03:13:47 -0400
  LV Status              available
  # open                 2
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:0

  --- Logical volume ---
  LV Path                /dev/ol/root
  LV Name                root
  VG Name                ol
  LV UUID                1xdvMc-tkPn-CsIM-vxwC-3uv8-fNZx-y0h1mf
  LV Write Access        read/write
  LV Creation host, time localhost, 2017-07-06 03:13:47 -0400
  LV Status              available
  # open                 1
  LV Size                77.51 GiB
  Current LE             19842
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           252:1
{% endcodeblock %}
8. Resize the file system 
[root@hktasu168 ~]# resize2fs /dev/mapper/ol-root
resize2fs 1.42.9 (28-Dec-2013)
resize2fs: Bad magic number in super-block while trying to open /dev/mapper/ol-root
Couldn't find valid filesystem superblock.
9. As Oracle Linux 7 is using `xfs` as default file system, so we need to install xfsprogs to resize the file system `yum install xfsprogs`. Then:
{% codeblock line_number:false lang:bash %}
[root@hktasu168 ~]# xfs_growfs /dev/mapper/ol-root
meta-data=/dev/mapper/ol-root    isize=256    agcount=4, agsize=1147392 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=4589568, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 4589568 to 20318208
[root@hktasu168 ~]# df -h
Filesystem           Size  Used Avail Use% Mounted on
/dev/mapper/ol-root   78G  7.8G   70G  10% /
devtmpfs             3.8G     0  3.8G   0% /dev
tmpfs                3.8G     0  3.8G   0% /dev/shm
tmpfs                3.8G  8.7M  3.8G   1% /run
tmpfs                3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/xvda1           497M  142M  356M  29% /boot
tmpfs                773M     0  773M   0% /run/user/0
{% endcodeblock %}
