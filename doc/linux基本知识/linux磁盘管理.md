# 磁盘管理相关命令

## 1. MBR分区
### 1.1 MBR结构
一个硬盘的存储结构（位置：0磁头0磁道1扇区）包括主引导记录、分区表、分区结束标志，以及其他分区的引导信息及数据区信息。
主引导记录：446字节
分区表：64字节（最多包含4个分区信息，每个分区信息占16字节空间）
分区结束标志：0x55AA 2字节

### 1.2 分区表内容
16字节分区表内容：

| 字节数 | 说明 |
| --- | --- |
| 1 B | State: 分区状态，0=未激活，0x80=激活 |
| 1 B | StartHead：分区起始磁头号 |
| 2 B | StartSC ：分区起始扇区和柱面号。低字节的低6位为扇区号，高2位为柱面的第9，10位；高字节为柱面号的低8位 |
| 1 B | Type : 分区类型，如0x0B=FAT32，0x83=Linux等，00表示此项未用 |
| 1 B | EndHead : 分区结束磁头号 |
| 2 B | EndSC : 分区结束扇区和柱面号 |
| 4 B | Relative :线性寻址方式下分区相对扇区地址（对于基本分区即为绝对地址） |
| 4 B | Sectors : 分区大小（总扇区数） |

fdisk,parted分区工具分区的实质就是改变上述64字节的分区表信息。 

### 1.3 分区方式：P主分区，E扩展分区
4P
3P+1E
2P+1E
1P+1E
（1）任意多个主分区，但要求1<=主分区数量<=4
（2）扩展分区可以和主分区组合，但要求2<=主分区+扩展分区数量<=4
（3）最多一个扩展分区，至少一个主分区，扩展分区不是一个真正的可用分区，建立完扩展分区后，还需要在扩展分区上建立逻辑分区才可以使用。
（4）分区数字编号1~4留给主分区或扩展分区使用，逻辑分区编号只能从5开始，即使1-4分区号即使有剩余，也不会分配给逻辑分区。

### 1.4 查看分区表od

```
[root@basic ~]# dd if=/dev/sda of=mbr.bin bs=512 count=1
1+0 records in
1+0 records out
512 bytes (512 B) copied, 0.000389388 s, 1.3 MB/s
[root@basic ~]# od -xa mbr.bin 
```

## 2. 分区工具
### 2.1 fdisk (适合于给小于2T的磁盘分区)
fdisk - Partition table manipulator for Linux
- linux下最常用的分区工具，一般是装完系统后进行分区。装系统之前的分区，多数都是系统ISO里面的分区分，或者raid里分小磁盘。
- fdisk只能root权限用户使用

命令：查看磁盘分区信息
  - fdisk -l [设备名称] 查看磁盘分区信息
```
[root@basic ~]# fdisk -l 
Disk /dev/sda: 10.7 GB, 10737418240 bytes
255 heads, 63 sectors/track, 1305 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0005d948

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          26      204800   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              26         157     1048576   82  Linux swap / Solaris
Partition 2 does not end on cylinder boundary.
/dev/sda3             157        1306     9231360   83  Linux
Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```

  - cat /proc/partitions
```
[root@basic ~]# cat /proc/partitions 
major minor  #blocks  name

   8        0   10485760 sda
   8        1     204800 sda1
   8        2    1048576 sda2
   8        3    9231360 sda3
   8       16    1048576 sdb
   8       17     102400 sdb1
```

分区实践：
``fdisk -cu /dev/sdb``
-cu   It's strongly recommended to switch off the mode (command 'c') and change display units to sectors (command 'u').
  -c 关闭dos兼容模式
  -u 使用扇区
```
[root@basic ~]# fdisk -cu /dev/sdb
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0xf168acbd.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition   // 删除分区
   l   list known partition types  // 显示系统当前所有磁盘分区
   m   print this menu      // 打印菜单
   n   add a new partition  // 创建新分区
   o   create a new empty DOS partition table
   p   print the partition table // 打印当前已分分区
   q   quit without saving changes  // 退出不保存分区表
   s   create a new empty Sun disklabel
   t   change a partition's system id  // 改变分区ID，不常用
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit // 退出保存分区表
   x   extra functionality (experts only)
```

``partprobe /dev/sdb1``
通知系统分区表已改变

### 2.2 parted（擅长给大于2T的磁盘分区，也可以对小于2T的磁盘分区）

交互式，先输入``parted /dev/sdb ``:
- mklabel gpt yes
- mkpart primary ext4 0 10 Ignore //创建主分区
- mkpart primary linux-swap 11 21 Ignore //创建swap分区
- mkpart logical ext4 22 32 Ignore  //创建逻辑分区
- p  // 显示分区表
- rm 1 //删除分区1

非交互式，命令行模式：
```
parted /dev/sdb mklabel gpt yes
parted /dev/sdb mkpart primary ext4 0 10 Ignore //创建主分区
parted /dev/sdb mkpart primary linux-swap 11 21 Ignore //创建swap分区
parted /dev/sdb mkpart logical ext4 22 32 Ignore  //创建逻辑分区
parted /dev/sdb p  // 显示分区表
```

gpt转换为mbr模式的命令：
```
parted /dev/sdb mklabel bsd yes
```

### 2.3 dd
dd - convert and copy a file
从磁盘创建一个文件，可以用来格式化成为其它文件系统并挂载

### 2.4 使用物理分区创建swap

```
fdisk -cu /dev/sdb -> n p 1 ... w 分成一个区
mkswap /dev/sdb1  //格式化为swap分区类型
free -m //显示内存信息
swapon /dev/sdb1  //挂载swap
free -m
swapoff /dev/sdb1 //取消挂载
free -m
```

### 2.5 使用文件构建swap
```
dd if=/dev/zero of=/tmp/swap bs=1M count=128
ls -l -h /tmp/swap
mkswap /tmp/swap
free -m
swapon /tmp/swap
free -m
swapon -s
swapoff /tmp/swap
free -m
```

### 2.6 查看文件系统内部细节（inode/block等）
``dumpe2fs /dev/sdb1``

## 3. 格式化分区/挂载 mkfs/mount
### 3.1 mkfs
mkfs - build a Linux file system
mkfs [-V] [-t fstype] [fs-options] filesys [blocks]
```
[root@basic ~]# mkfs
mkfs          mkfs.ext2     mkfs.ext4     mkfs.msdos    
mkfs.cramfs   mkfs.ext3     mkfs.ext4dev  mkfs.vfat 
```
mkfs -t ext4 -b 4096 -I 256 /dev/sdb1等价于mkfs.ext4 -b 4096 -I 256 /dev/sdb1
参数：
 -t 文件系统格式
 -b block大小
 -I inode大小

注意：tune2fs -c -l /dev/sdb1 //去除下述信息，不做自动检查
```
This filesystem will be automatically checked every 33 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
```

### 3.2 mount挂载
mount - mount a filesystem
- 挂载
mount -t ext4 /dev/sdb1 /mnt
参数：
  -t 指定文件系统类型
  -o 选项

- 解除挂载：
umount /mnt  [-lf] //-f强制解除挂载

- 查看挂载：
```
mount
cat /proc/mounts
```

- 开机挂载/etc/fstab及/etc/mtab
将/dev/sdb2每次开机都自动挂载到/tmp/sdb2
```
编辑/etc/fstab
写入： /dev/sdb2  /tmp/sdb2  ext3    defaults         0         0
         设备         挂载点    类型   挂载参数选项      是否备份  是否开机进行磁盘检查
```