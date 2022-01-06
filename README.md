# devops-netology

### «3.5. Файловые системы»

1. Узнайте о sparse (разряженных) файлах.

   ###### Почитал, правильнее было бы в вопросе писать через "е", а не через "я", наверное :)
2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?

   ###### Пожалуй, не могут. Т.к. жесткая ссылка - это по сути ссылка на тот же самый объект с тем же inode, то права будут идентичными.
3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:


         Vagrant.configure("2") do |config|
           config.vm.box = "bento/ubuntu-20.04"
           config.vm.provider :virtualbox do |vb|
             lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
             lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
             vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
             vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
             vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
             vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
           end
         end
Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.

###### Вот что получилось:

         vagrant@vagrant:~$ lsblk
         NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
         loop1                       7:1    0 70.3M  1 loop /snap/lxd/21029
         loop2                       7:2    0 32.3M  1 loop /snap/snapd/12704
         loop3                       7:3    0 43.3M  1 loop /snap/snapd/14295
         loop4                       7:4    0 55.5M  1 loop /snap/core18/2253
         loop5                       7:5    0 61.9M  1 loop /snap/core20/1270
         loop6                       7:6    0 67.2M  1 loop /snap/lxd/21835
         sda                         8:0    0   64G  0 disk
         ├─sda1                      8:1    0    1M  0 part
         ├─sda2                      8:2    0    1G  0 part /boot
         └─sda3                      8:3    0   63G  0 part
           └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
         sdb                         8:16   0  2.5G  0 disk
         sdc                         8:32   0  2.5G  0 disk


4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.


		root@vagrant:/home/vagrant# fdisk /dev/sdb
		
		Welcome to fdisk (util-linux 2.34).
		Changes will remain in memory only, until you decide to write them.
		Be careful before using the write command.
		
		Device does not contain a recognized partition table.
		Created a new DOS disklabel with disk identifier 0x068b515b.
		
		Command (m for help):
		
		
		Command (m for help): n
		Partition type
		   p   primary (0 primary, 0 extended, 4 free)
		   e   extended (container for logical partitions)
		Select (default p):
		
		Using default response p.
		Partition number (1-4, default 1):
		First sector (2048-5242879, default 2048):
		Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G
		
		Created a new partition 1 of type 'Linux' and of size 2 GiB.
		
		Command (m for help): n
		Partition type
		   p   primary (1 primary, 0 extended, 3 free)
		   e   extended (container for logical partitions)
		Select (default p):
		
		Using default response p.
		Partition number (2-4, default 2):
		First sector (4196352-5242879, default 4196352):
		Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879):
		
		Created a new partition 2 of type 'Linux' and of size 511 MiB.
		
		Command (m for help): w
		The partition table has been altered.
		Calling ioctl() to re-read partition table.
		Syncing disks.
		root@vagrant:/home/vagrant# lsblk
		NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
		loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
		loop1                       7:1    0 70.3M  1 loop /snap/lxd/21029
		loop2                       7:2    0 32.3M  1 loop /snap/snapd/12704
		loop3                       7:3    0 43.3M  1 loop /snap/snapd/14295
		loop4                       7:4    0 55.5M  1 loop /snap/core18/2253
		loop5                       7:5    0 61.9M  1 loop /snap/core20/1270
		loop6                       7:6    0 67.2M  1 loop /snap/lxd/21835
		sda                         8:0    0   64G  0 disk
		├─sda1                      8:1    0    1M  0 part
		├─sda2                      8:2    0    1G  0 part /boot
		└─sda3                      8:3    0   63G  0 part
		  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
		sdb                         8:16   0  2.5G  0 disk
		├─sdb1                      8:17   0    2G  0 part
		└─sdb2                      8:18   0  511M  0 part
		sdc                         8:32   0  2.5G  0 disk
		root@vagrant:/home/vagrant#



5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.


		root@vagrant:/home/vagrant# sfdisk -d /dev/sdb | sfdisk --force /dev/sdc
		Checking that no-one is using this disk right now ... OK
		
		Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
		Disk model: VBOX HARDDISK
		Units: sectors of 1 * 512 = 512 bytes
		Sector size (logical/physical): 512 bytes / 512 bytes
		I/O size (minimum/optimal): 512 bytes / 512 bytes
		
		>>> Script header accepted.
		>>> Script header accepted.
		>>> Script header accepted.
		>>> Script header accepted.
		>>> Created a new DOS disklabel with disk identifier 0x068b515b.
		/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
		/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
		/dev/sdc3: Done.
		
		New situation:
		Disklabel type: dos
		Disk identifier: 0x068b515b
		
		Device     Boot   Start     End Sectors  Size Id Type
		/dev/sdc1          2048 4196351 4194304    2G 83 Linux
		/dev/sdc2       4196352 5242879 1046528  511M 83 Linux
		
		The partition table has been altered.
		Calling ioctl() to re-read partition table.
		Syncing disks.
		root@vagrant:/home/vagrant#

Проверим, что получилось:

		root@vagrant:/home/vagrant# fdisk -l /dev/sdb; fdisk -l /dev/sdc
		Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
		Disk model: VBOX HARDDISK
		Units: sectors of 1 * 512 = 512 bytes
		Sector size (logical/physical): 512 bytes / 512 bytes
		I/O size (minimum/optimal): 512 bytes / 512 bytes
		Disklabel type: dos
		Disk identifier: 0x068b515b
		
		Device     Boot   Start     End Sectors  Size Id Type
		/dev/sdb1          2048 4196351 4194304    2G 83 Linux
		/dev/sdb2       4196352 5242879 1046528  511M 83 Linux
		Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
		Disk model: VBOX HARDDISK
		Units: sectors of 1 * 512 = 512 bytes
		Sector size (logical/physical): 512 bytes / 512 bytes
		I/O size (minimum/optimal): 512 bytes / 512 bytes
		Disklabel type: dos
		Disk identifier: 0x068b515b
		
		Device     Boot   Start     End Sectors  Size Id Type
		/dev/sdc1          2048 4196351 4194304    2G 83 Linux
		/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

6. Соберите mdadm RAID1 на паре разделов 2 Гб.


		root@vagrant:/home/vagrant# mdadm --create --verbose /dev/md0 -l 1 -n 2 /dev/sd{b1,c1}
		mdadm: Note: this array has metadata at the start and
		    may not be suitable as a boot device.  If you plan to
		    store '/boot' on this device please ensure that
		    your boot-loader understands md/v1.x metadata, or use
		    --metadata=0.90
		mdadm: size set to 2094080K
		Continue creating array? yes
		mdadm: Defaulting to version 1.2 metadata
		mdadm: array /dev/md0 started.

7. Соберите mdadm RAID0 на второй паре маленьких разделов.

	
		root@vagrant:/home/vagrant# mdadm --create --verbose /dev/md1 -l 0 -n 2 /dev/sd{b2,c2}
		mdadm: chunk size defaults to 512K
		mdadm: Defaulting to version 1.2 metadata
		mdadm: array /dev/md1 started.
		root@vagrant:/home/vagrant# lsblk
		NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
		loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
		loop1                       7:1    0 70.3M  1 loop  /snap/lxd/21029
		loop2                       7:2    0 32.3M  1 loop  /snap/snapd/12704
		loop3                       7:3    0 43.3M  1 loop  /snap/snapd/14295
		loop4                       7:4    0 55.5M  1 loop  /snap/core18/2253
		loop5                       7:5    0 61.9M  1 loop  /snap/core20/1270
		loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
		sda                         8:0    0   64G  0 disk
		├─sda1                      8:1    0    1M  0 part
		├─sda2                      8:2    0    1G  0 part  /boot
		└─sda3                      8:3    0   63G  0 part
		  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
		sdb                         8:16   0  2.5G  0 disk
		├─sdb1                      8:17   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		└─sdb2                      8:18   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		sdc                         8:32   0  2.5G  0 disk
		├─sdc1                      8:33   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		└─sdc2                      8:34   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		root@vagrant:/home/vagrant#

8. Создайте 2 независимых PV на получившихся md-устройствах.


		root@vagrant:/home/vagrant# pvcreate /dev/md0 /dev/md1
		  Physical volume "/dev/md0" successfully created.
		  Physical volume "/dev/md1" successfully created.
		root@vagrant:/home/vagrant#

9. Создайте общую volume-group на этих двух PV.


		root@vagrant:/home/vagrant# vgcreate volgr1 /dev/md0 /dev/md1
		  Volume group "volgr1" successfully created
		root@vagrant:/home/vagrant# vgdisplay
		  --- Volume group ---
		  VG Name               ubuntu-vg
		  System ID
		  Format                lvm2
		  Metadata Areas        1
		  Metadata Sequence No  2
		  VG Access             read/write
		  VG Status             resizable
		  MAX LV                0
		  Cur LV                1
		  Open LV               1
		  Max PV                0
		  Cur PV                1
		  Act PV                1
		  VG Size               <63.00 GiB
		  PE Size               4.00 MiB
		  Total PE              16127
		  Alloc PE / Size       8064 / 31.50 GiB
		  Free  PE / Size       8063 / <31.50 GiB
		  VG UUID               aK7Bd1-JPle-i0h7-5jJa-M60v-WwMk-PFByJ7
		
		  --- Volume group ---
		  VG Name               volgr1
		  System ID
		  Format                lvm2
		  Metadata Areas        2
		  Metadata Sequence No  1
		  VG Access             read/write
		  VG Status             resizable
		  MAX LV                0
		  Cur LV                0
		  Open LV               0
		  Max PV                0
		  Cur PV                2
		  Act PV                2
		  VG Size               <2.99 GiB
		  PE Size               4.00 MiB
		  Total PE              765
		  Alloc PE / Size       0 / 0
		  Free  PE / Size       765 / <2.99 GiB
		  VG UUID               tVNZDn-aOr2-bpte-HM22-uCUw-ZipG-rLtUGl
		
		root@vagrant:/home/vagrant#

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.


		root@vagrant:/home/vagrant# lvcreate -L 100M volgr1 /dev/md1
		  Logical volume "lvol0" created.
		root@vagrant:/home/vagrant# vgs
		  VG        #PV #LV #SN Attr   VSize   VFree
		  ubuntu-vg   1   1   0 wz--n- <63.00g <31.50g
		  volgr1      2   1   0 wz--n-  <2.99g   2.89g
		root@vagrant:/home/vagrant# lvs
		  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
		  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
		  lvol0     volgr1    -wi-a----- 100.00m
		root@vagrant:/home/vagrant#

11. Создайте mkfs.ext4 ФС на получившемся LV.


		root@vagrant:/home/vagrant# mkfs.ext4 /dev/volgr1/lvol0
		mke2fs 1.45.5 (07-Jan-2020)
		Creating filesystem with 25600 4k blocks and 25600 inodes
		
		Allocating group tables: done
		Writing inode tables: done
		Creating journal (1024 blocks): done
		Writing superblocks and filesystem accounting information: done
		
		root@vagrant:/home/vagrant#
		root@vagrant:/home/vagrant#
		root@vagrant:/home/vagrant# lsblk -o NAME,PATH,SIZE,FSTYPE
		NAME                      PATH                               SIZE FSTYPE
		loop0                     /dev/loop0                        55.4M squashfs
		loop1                     /dev/loop1                        70.3M squashfs
		loop2                     /dev/loop2                        32.3M squashfs
		loop3                     /dev/loop3                        43.3M squashfs
		loop4                     /dev/loop4                        55.5M squashfs
		loop5                     /dev/loop5                        61.9M squashfs
		loop6                     /dev/loop6                        67.2M squashfs
		sda                       /dev/sda                            64G
		├─sda1                    /dev/sda1                            1M
		├─sda2                    /dev/sda2                            1G ext4
		└─sda3                    /dev/sda3                           63G LVM2_member
		  └─ubuntu--vg-ubuntu--lv /dev/mapper/ubuntu--vg-ubuntu--lv 31.5G ext4
		sdb                       /dev/sdb                           2.5G
		├─sdb1                    /dev/sdb1                            2G linux_raid_member
		│ └─md0                   /dev/md0                             2G LVM2_member
		└─sdb2                    /dev/sdb2                          511M linux_raid_member
		  └─md1                   /dev/md1                          1018M LVM2_member
		    └─volgr1-lvol0        /dev/mapper/volgr1-lvol0           100M ext4
		sdc                       /dev/sdc                           2.5G
		├─sdc1                    /dev/sdc1                            2G linux_raid_member
		│ └─md0                   /dev/md0                             2G LVM2_member
		└─sdc2                    /dev/sdc2                          511M linux_raid_member
		  └─md1                   /dev/md1                          1018M LVM2_member
		    └─volgr1-lvol0        /dev/mapper/volgr1-lvol0           100M ext4
		root@vagrant:/home/vagrant#

12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.


		root@vagrant:/home/vagrant# mkdir /tmp/new
		root@vagrant:/home/vagrant# mount /dev/volgr1/lvol0 /tmp/new
		root@vagrant:/home/vagrant#

13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.


		root@vagrant:/home/vagrant# ll /tmp/new
		total 24
		drwxr-xr-x  3 root root  4096 Jan  6 14:57 ./
		drwxrwxrwt 12 root root  4096 Jan  6 15:02 ../
		drwx------  2 root root 16384 Jan  6 14:57 lost+found/
		root@vagrant:/home/vagrant# wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
		--2022-01-06 15:06:11--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
		Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183, 2a02:6b8::183
		Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
		HTTP request sent, awaiting response... 200 OK
		Length: 21580969 (21M) [application/octet-stream]
		Saving to: ‘/tmp/new/test.gz’
		
		/tmp/new/test.gz              100%[=================================================>]  20.58M  5.73MB/s    in 3.4s
		
		2022-01-06 15:06:14 (6.09 MB/s) - ‘/tmp/new/test.gz’ saved [21580969/21580969]
		
		root@vagrant:/home/vagrant# ll /tmp/new
		total 21100
		drwxr-xr-x  3 root root     4096 Jan  6 15:06 ./
		drwxrwxrwt 12 root root     4096 Jan  6 15:02 ../
		drwx------  2 root root    16384 Jan  6 14:57 lost+found/
		-rw-r--r--  1 root root 21580969 Jan  6 11:03 test.gz
		root@vagrant:/home/vagrant#

14. Прикрепите вывод lsblk.


		root@vagrant:/home/vagrant# lsblk
		NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
		loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
		loop1                       7:1    0 70.3M  1 loop  /snap/lxd/21029
		loop2                       7:2    0 32.3M  1 loop  /snap/snapd/12704
		loop3                       7:3    0 43.3M  1 loop  /snap/snapd/14295
		loop4                       7:4    0 55.5M  1 loop  /snap/core18/2253
		loop5                       7:5    0 61.9M  1 loop  /snap/core20/1270
		loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
		sda                         8:0    0   64G  0 disk
		├─sda1                      8:1    0    1M  0 part
		├─sda2                      8:2    0    1G  0 part  /boot
		└─sda3                      8:3    0   63G  0 part
		  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
		sdb                         8:16   0  2.5G  0 disk
		├─sdb1                      8:17   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		└─sdb2                      8:18   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		    └─volgr1-lvol0        253:1    0  100M  0 lvm   /tmp/new
		sdc                         8:32   0  2.5G  0 disk
		├─sdc1                      8:33   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		└─sdc2                      8:34   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		    └─volgr1-lvol0        253:1    0  100M  0 lvm   /tmp/new
		root@vagrant:/home/vagrant#

15. Протестируйте целостность файла:


		root@vagrant:~# gzip -t /tmp/new/test.gz
		root@vagrant:~# echo $?
		0

Собственно вот:

		root@vagrant:/home/vagrant# gzip -t /tmp/new/test.gz; echo $?
		0
		root@vagrant:/home/vagrant#

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.


		root@vagrant:/home/vagrant# pvmove /dev/md1
		  /dev/md1: Moved: 16.00%
		  /dev/md1: Moved: 100.00%
		root@vagrant:/home/vagrant# lsblk
		NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
		loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
		loop1                       7:1    0 70.3M  1 loop  /snap/lxd/21029
		loop2                       7:2    0 32.3M  1 loop  /snap/snapd/12704
		loop3                       7:3    0 43.3M  1 loop  /snap/snapd/14295
		loop4                       7:4    0 55.5M  1 loop  /snap/core18/2253
		loop5                       7:5    0 61.9M  1 loop  /snap/core20/1270
		loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
		sda                         8:0    0   64G  0 disk
		├─sda1                      8:1    0    1M  0 part
		├─sda2                      8:2    0    1G  0 part  /boot
		└─sda3                      8:3    0   63G  0 part
		  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
		sdb                         8:16   0  2.5G  0 disk
		├─sdb1                      8:17   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		│   └─volgr1-lvol0        253:1    0  100M  0 lvm   /tmp/new
		└─sdb2                      8:18   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		sdc                         8:32   0  2.5G  0 disk
		├─sdc1                      8:33   0    2G  0 part
		│ └─md0                     9:0    0    2G  0 raid1
		│   └─volgr1-lvol0        253:1    0  100M  0 lvm   /tmp/new
		└─sdc2                      8:34   0  511M  0 part
		  └─md1                     9:1    0 1018M  0 raid0
		root@vagrant:/home/vagrant#

17. Сделайте --fail на устройство в вашем RAID1 md.


		root@vagrant:/home/vagrant# mdadm /dev/md0 --fail /dev/sdb1
		mdadm: set /dev/sdb1 faulty in /dev/md0
		root@vagrant:/home/vagrant# mdadm --detail /dev/md0
		/dev/md0:
		           Version : 1.2
		     Creation Time : Thu Jan  6 14:41:32 2022
		        Raid Level : raid1
		        Array Size : 2094080 (2045.00 MiB 2144.34 MB)
		     Used Dev Size : 2094080 (2045.00 MiB 2144.34 MB)
		      Raid Devices : 2
		     Total Devices : 2
		       Persistence : Superblock is persistent
		
		       Update Time : Thu Jan  6 15:26:01 2022
		             State : clean, degraded
		    Active Devices : 1
		   Working Devices : 1
		    Failed Devices : 1
		     Spare Devices : 0
		
		Consistency Policy : resync
		
		              Name : vagrant:0  (local to host vagrant)
		              UUID : 8bba7538:86500e04:60d17f72:7f27c8e5
		            Events : 19
		
		    Number   Major   Minor   RaidDevice State
		       -       0        0        0      removed
		       1       8       33        1      active sync   /dev/sdc1
		
		       0       8       17        -      faulty   /dev/sdb1
		root@vagrant:/home/vagrant#

18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.


		root@vagrant:/home/vagrant#
		root@vagrant:/home/vagrant# dmesg | grep md0
		[33671.396290] md/raid1:md0: not clean -- starting background reconstruction
		[33671.396293] md/raid1:md0: active with 2 out of 2 mirrors
		[33671.396477] md0: detected capacity change from 0 to 2144337920
		[33671.399497] md: resync of RAID array md0
		[33681.999556] md: md0: resync done.
		[36340.285714] md/raid1:md0: Disk failure on sdb1, disabling device.
		               md/raid1:md0: Operation continuing on 1 devices.
		root@vagrant:/home/vagrant#

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:


          root@vagrant:~# gzip -t /tmp/new/test.gz
          root@vagrant:~# echo $?
          0

Ok:

		root@vagrant:/home/vagrant# gzip -t /tmp/new/test.gz; echo $?
		0
		root@vagrant:/home/vagrant#

21. Погасите тестовый хост, vagrant destroy.


		C:\Users\Kashnerok\Documents\net_do>vagrant destroy
		    default: Are you sure you want to destroy the 'default' VM? [y/N] y
		==> default: Forcing shutdown of VM...
		==> default: Destroying VM and associated drives...
		
		C:\Users\Kashnerok\Documents\net_do>

 