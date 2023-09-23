Title: One USB Drive, Two Partitions. Much useful.
Date: 2014-02-04 12:39
Author: Jacob Morgan
Category: Operations
Tags: configuration, tools, fat32, unetbootin, fdisk
Slug: one-usb-drive-two-partitions-much-useful

In a follow up to the [Dogecoin client install][] post, here is steps to
create multiple partitons on a single USB drive so one can both boot and
store files on the same USB drive.

Partition the USB drive. I made two partitions. One 2GB FAT32, and
28.5GB ext4. I initially did this for Arch Linux, but added/changed to
Ubuntu for dogecoin users. A few of the steps below when partitioning
are not actually needed when you use unetbootin to write the iso,
however there is no harm in doing them.

    :::bash  
    [jmorgan@arch-dopey ~]$ sudo fdisk /dev/sdc
    
    Welcome to fdisk (util-linux 2.24).  
    Changes will remain in memory only, until you decide to write them.  
    Be careful before using the write command.
    
    #Current (Empty)  
    Command (m for help): p  
    Disk /dev/sdc: 30.5 GiB, 32717537280 bytes, 63901440 sectors  
    Units: sectors of 1 * 512 = 512 bytes  
    Sector size (logical/physical): 512 bytes / 512 bytes  
    I/O size (minimum/optimal): 512 bytes / 512 bytes  
    Disklabel type: dos  
    Disk identifier: 0xc3072e18
    
    #Create 2GB Partition  
    Command (m for help): n
    
    Partition type:  
    p primary (0 primary, 0 extended, 4 free)  
    e extended  
    Select (default p): p  
    Partition number (1-4, default 1):  
    First sector (2048-63901439, default 2048):  
    Last sector, +sectors or +size{K,M,G,T,P} (2048-63901439, default
    63901439): +2GB
    
    Created a new partition 1 of type 'Linux' and of size 1.9 GiB.  
    #Change type  
    Command (m for help): t  
    Selected partition 1  
    Hex code (type L to list all codes): L
    
    0 Empty 24 NEC DOS 81 Minix / old Lin bf Solaris  
    1 FAT12 27 Hidden NTFS Win 82 Linux swap / So c1 DRDOS/sec (FAT-  
    2 XENIX root 39 Plan 9 83 Linux c4 DRDOS/sec (FAT-  
    3 XENIX usr 3c PartitionMagic 84 OS/2 hidden C: c6 DRDOS/sec (FAT-  
    4 FAT16 \<32M 40 Venix 80286 85 Linux extended c7 Syrinx  
    5 Extended 41 PPC PReP Boot 86 NTFS volume set da Non-FS data  
    6 FAT16 42 SFS 87 NTFS volume set db CP/M / CTOS / .  
    7 HPFS/NTFS/exFAT 4d QNX4.x 88 Linux plaintext de Dell Utility  
    8 AIX 4e QNX4.x 2nd part 8e Linux LVM df BootIt  
    9 AIX bootable 4f QNX4.x 3rd part 93 Amoeba e1 DOS access  
    a OS/2 Boot Manag 50 OnTrack DM 94 Amoeba BBT e3 DOS R/O  
    b W95 FAT32 51 OnTrack DM6 Aux 9f BSD/OS e4 SpeedStor  
    c W95 FAT32 (LBA) 52 CP/M a0 IBM Thinkpad hi eb BeOS fs  
    e W95 FAT16 (LBA) 53 OnTrack DM6 Aux a5 FreeBSD ee GPT  
    f W95 Ext'd (LBA) 54 OnTrackDM6 a6 OpenBSD ef EFI (FAT-12/16/  
    10 OPUS 55 EZ-Drive a7 NeXTSTEP f0 Linux/PA-RISC b  
    11 Hidden FAT12 56 Golden Bow a8 Darwin UFS f1 SpeedStor  
    12 Compaq diagnost 5c Priam Edisk a9 NetBSD f4 SpeedStor  
    14 Hidden FAT16 \<3 61 SpeedStor ab Darwin boot f2 DOS secondary  
    16 Hidden FAT16 63 GNU HURD or Sys af HFS / HFS+ fb VMware VMFS  
    17 Hidden HPFS/NTF 64 Novell Netware b7 BSDI fs fc VMware VMKCORE  
    18 AST SmartSleep 65 Novell Netware b8 BSDI swap fd Linux raid auto  
    1b Hidden W95 FAT3 70 DiskSecure Mult bb Boot Wizard hid fe LANstep  
    1c Hidden W95 FAT3 75 PC/IX be Solaris boot ff BBT  
    1e Hidden W95 FAT1 80 Old Minix  
    #b is FAT32  
    Hex code (type L to list all codes): b  
    If you have created or modified any DOS 6.x partitions, please see the
    fdisk documentation for additional information.  
    Changed type of partition 'Linux' to 'W95 FAT32'.
    
    #Change bootable flag  
    Command (m for help): a  
    Selected partition 1  
    #Create 28.5GB partition  
    Command (m for help): n
    
    Partition type:  
    p primary (1 primary, 0 extended, 3 free)  
    e extended  
    Select (default p):
    
    Using default response p.  
    Partition number (2-4, default 2):  
    First sector (3907584-63901439, default 3907584):  
    Last sector, +sectors or +size{K,M,G,T,P} (3907584-63901439, default
    63901439):
    
    Created a new partition 2 of type 'Linux' and of size 28.6 GiB.  
    #Write partition table  
    Command (m for help): w  
    The partition table has been altered.  
    Calling ioctl() to re-read partition table.  
    Syncing disks.
    
    [jmorgan@arch-dopey ~]$  
    
Create the filesystems, FAT32 and ext4 for me.  

    :::bash  
    #FAT32 -- For Arch bootable iso, this label must match the release
    version used -- Not important for Ubuntu/unetbootin  
    [jmorgan@arch-dopey ~]$ sudo mkdosfs -F 32 -n ARCH_201401 /dev/sdc1  
    mkfs.fat 3.0.24 (2013-11-23)  
    #ext4  
    [jmorgan@arch-dopey ~]$ sudo mkfs.ext4 /dev/sdc2  
    mke2fs 1.42.8 (20-Jun-2013)  
    Filesystem label=  
    OS type: Linux  
    Block size=4096 (log=2)  
    Fragment size=4096 (log=2)  
    Stride=0 blocks, Stripe width=0 blocks  
    1875968 inodes, 7499232 blocks  
    374961 blocks (5.00%) reserved for the super user  
    First data block=0  
    Maximum filesystem blocks=4294967296  
    229 block groups  
    32768 blocks per group, 32768 fragments per group  
    8192 inodes per group  
    Superblock backups stored on blocks:  
    32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632,
    2654208,  
    4096000
    
    Allocating group tables: done  
    Writing inode tables: done  
    Creating journal (32768 blocks): done  
    Writing superblocks and filesystem accounting information: done
    
    [jmorgan@arch-dopey ~]$  

At this point you have a single USB drive with two partitions. One with
the intent of being used for bootable image(Ubuntu, Arch, etc) and the
other for storing data.

If you are reading this post for Dogecoin I suggest using unetbootin
along with Ubuntu 12.04 iso. Select the Ubuntu iso, and specify the
smaller(first) partition to write to. You can store the needed files for
the client install on the other partition. Here is how.

You can let unetbootin download the iso for you, or in my case use a
local iso. I've already downloaded Ubuntu 64-bit 12.04. I've selected
this iso, and the first partition on the USB drive(sdc1).

[![unetbootin][]][unetbootin]

Checking what we've done, and storing needed files onto the second
partition. 
    
    :::bash  
    #mount the partiton unetbootin wrote Ubuntu 12.04 to:  
    [jmorgan@arch-dopey ~]$ sudo mount /dev/sdc1 /mnt/os  
    #mount the partition used for storage.  
    [jmorgan@arch-dopey ~]$ sudo mount /dev/sdc2 /mnt/files  
    #contents of what unetbootin created  
    [jmorgan@arch-dopey ~]$ ls /mnt/os/  
    arch dists ldlinux.c32 menu.c32 README.diskdefines ubninit  
    autorun.inf EFI ldlinux.sys pics syslinux ubnkern  
    boot install loader pool syslinux.cfg ubnpathl.txt  
    casper isolinux md5sum.txt preseed ubnfilel.txt wubi.exe  
    [jmorgan@arch-dopey ~]$
    
    #copying dogecoin-qt tar with deps, and script to build dogecoin-qt
    after booting Ubuntu 12.04.  
    [jmorgan@arch-dopey ~]$ sudo cp
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install.tar /mnt/files/  
    [jmorgan@arch-dopey ~]$ ls
    /mnt/files/dogecoin-qt-1.5-ubuntu-12.04-Live-Install.tar  
    /mnt/files/dogecoin-qt-1.5-ubuntu-12.04-Live-Install.tar  
    [jmorgan@arch-dopey ~]$  

At this point you can follow the previous post, [Dogecoin Ubuntu Live CD
Installer][Dogecoin client install]. I personally carry a USB bottle
opener my sister gave me which has a bootable linux distro on one
partition, and storage on the other. At any time I can open a beer, and
boot linux!In that order ofcourse. :)

Doge: D8kWKgF2x6gsiZzRyXgonogPQuRMxf6qtp

  [Dogecoin client install]: http://jpyth.com/?p=225
    "Dogecoin Ubuntu Live CD Installer"
  [unetbootin]: /images/unetbootin.png
