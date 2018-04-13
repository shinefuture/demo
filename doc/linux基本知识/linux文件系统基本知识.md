# linux文件系统

操作系统中的数据分为文件内容和文件属性两部分。
其中，文件内容就是文件的实体数据（block里）；文件属性就是文件类型、权限、属主、修改时间等信息（inode里）。
操作系统会将文件属性放入磁盘文件系统的inode里，实体数据存放于对应的block中。
除此之外，操作系统还会记录文件系统的整体信息于superblock里。
superblock包括整个文件系统的inode和block的总数量、已经使用的数量、剩余数量等（称之为metadata元数据）。


## 1. inode
i节点(inode)是用来存储文件的属性信息
```
文件的拥有者和所属用户组
文件的访问权限设定
文件的类型
文件的访问、修改时间等
文件的大小
文件的各种标志，如SUID和SGID等
指向文件内容数据块的指针
```
一个inode的大小通常为128字节(ext4中的inode大小扩展到256字节)。
inode表用于跟踪定位每个文件，包括位置、大小等（但不包括文件名，文件名包含于dentry结构中），一个块组只有一个inode表。


## 2. block
数据存储的实体。


## 3. ext2文件系统的组成
- 文件系统结构：
超级块  块组1 块组2 块组3 ...

- 块组结构：
组描述  block位图  inode位图  inode区  block区

概念解释：
超级块：记录文件系统的控制与管理信息
  - block和inode的个数和大小，及其已用和未用数量
  - 文件系统的载入时间、最近一次写入数据时间、最近一次执行fsck进行磁盘检查的时间等
  - 有效位：0表示已载入，1表示未载入

组描述：记录本块组从何处开始
block位图：使用位图记录哪些块已用，哪些块未用
inode位图：使用位图记录哪些inode已用，哪些未用
inode区：各inode组成的区域，每个inode都有一个唯一编号
block区：各block组成的区域，用于存放文件数据

查看文件系统详细信息：
``dumpe2fs /dev/sdb1``
```
[root@basic ~]# dumpe2fs /dev/sdb1
dumpe2fs 1.41.12 (17-May-2010)
Filesystem volume name:   <none>
Last mounted on:          <not available>
Filesystem UUID:          66cd9199-74e4-4b61-87b4-f9f964fdf0f6
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype extent flex_bg sparse_super huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash 
Default mount options:    (none)
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              25688
Block count:              102400
Reserved block count:     5120
Free blocks:              93504
Free inodes:              25677
First block:              1
Block size:               1024
Fragment size:            1024
Reserved GDT blocks:      256
Blocks per group:         8192
Fragments per group:      8192
Inodes per group:         1976
Inode blocks per group:   247
Flex block group size:    16
Filesystem created:       Tue Apr  3 03:11:33 2018
Last mount time:          Tue Apr  3 03:29:23 2018
Last write time:          Tue Apr  3 03:29:47 2018
Mount count:              2
Maximum mount count:      33
Last checked:             Tue Apr  3 03:11:33 2018
Check interval:           15552000 (6 months)
Next check after:         Sun Sep 30 03:11:33 2018
Lifetime writes:          7687 kB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               128
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      37a23b1d-dfb2-4fdb-a7e7-80cc3660d78b
Journal backup:           inode blocks
Journal features:         (none)
Journal size:             4096k
Journal length:           4096
Journal sequence:         0x00000004
Journal start:            0


Group 0: (Blocks 1-8192) [ITABLE_ZEROED]
  Checksum 0xb5f4, unused inodes 1965
  Primary superblock at 1, Group descriptors at 2-2
  Reserved GDT blocks at 3-258
  Block bitmap at 259 (+258), Inode bitmap at 275 (+274)
  Inode table at 291-537 (+290)
  4683 free blocks, 1965 free inodes, 2 directories, 1965 unused inodes
  Free blocks: 3510-8192
  Free inodes: 12-1976
Group 1: (Blocks 8193-16384) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0xbe8b, unused inodes 1976
  Backup superblock at 8193, Group descriptors at 8194-8194
  Reserved GDT blocks at 8195-8450
  Block bitmap at 260 (+4294959363), Inode bitmap at 276 (+4294959379)
  Inode table at 538-784 (+4294959641)
  7934 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 8451-16384
  Free inodes: 1977-3952
Group 2: (Blocks 16385-24576) [INODE_UNINIT, BLOCK_UNINIT, ITABLE_ZEROED]
  Checksum 0x7740, unused inodes 1976
  Block bitmap at 261 (+4294951172), Inode bitmap at 277 (+4294951188)
  Inode table at 785-1031 (+4294951696)
  8192 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 16385-24576
  Free inodes: 3953-5928
Group 3: (Blocks 24577-32768) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0x89af, unused inodes 1976
  Backup superblock at 24577, Group descriptors at 24578-24578
  Reserved GDT blocks at 24579-24834
  Block bitmap at 262 (+4294942981), Inode bitmap at 278 (+4294942997)
  Inode table at 1032-1278 (+4294943751)
  7934 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 24835-32768
  Free inodes: 5929-7904
Group 4: (Blocks 32769-40960) [INODE_UNINIT, BLOCK_UNINIT, ITABLE_ZEROED]
  Checksum 0xfb46, unused inodes 1976
  Block bitmap at 263 (+4294934790), Inode bitmap at 279 (+4294934806)
  Inode table at 1279-1525 (+4294935806)
  8192 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 32769-40960
  Free inodes: 7905-9880
Group 5: (Blocks 40961-49152) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0x2ef8, unused inodes 1976
  Backup superblock at 40961, Group descriptors at 40962-40962
  Reserved GDT blocks at 40963-41218
  Block bitmap at 264 (+4294926599), Inode bitmap at 280 (+4294926615)
  Inode table at 1526-1772 (+4294927861)
  7934 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 41219-49152
  Free inodes: 9881-11856
Group 6: (Blocks 49153-57344) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0xc0ec, unused inodes 1976
  Block bitmap at 265 (+4294918408), Inode bitmap at 281 (+4294918424)
  Inode table at 1773-2019 (+4294919916)
  4096 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 53249-57344
  Free inodes: 11857-13832
Group 7: (Blocks 57345-65536) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0x9d99, unused inodes 1976
  Backup superblock at 57345, Group descriptors at 57346-57346
  Reserved GDT blocks at 57347-57602
  Block bitmap at 266 (+4294910217), Inode bitmap at 282 (+4294910233)
  Inode table at 2020-2266 (+4294911971)
  7934 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 57603-65536
  Free inodes: 13833-15808
Group 8: (Blocks 65537-73728) [INODE_UNINIT, BLOCK_UNINIT, ITABLE_ZEROED]
  Checksum 0x02c8, unused inodes 1976
  Block bitmap at 267 (+4294902026), Inode bitmap at 283 (+4294902042)
  Inode table at 2267-2513 (+4294904026)
  8192 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 65537-73728
  Free inodes: 15809-17784
Group 9: (Blocks 73729-81920) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0xa8b2, unused inodes 1976
  Backup superblock at 73729, Group descriptors at 73730-73730
  Reserved GDT blocks at 73731-73986
  Block bitmap at 268 (+4294893835), Inode bitmap at 284 (+4294893851)
  Inode table at 2514-2760 (+4294896081)
  7934 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 73987-81920
  Free inodes: 17785-19760
Group 10: (Blocks 81921-90112) [INODE_UNINIT, BLOCK_UNINIT, ITABLE_ZEROED]
  Checksum 0x8e4b, unused inodes 1976
  Block bitmap at 269 (+4294885644), Inode bitmap at 285 (+4294885660)
  Inode table at 2761-3007 (+4294888136)
  8192 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 81921-90112
  Free inodes: 19761-21736
Group 11: (Blocks 90113-98304) [INODE_UNINIT, BLOCK_UNINIT, ITABLE_ZEROED]
  Checksum 0x680b, unused inodes 1976
  Block bitmap at 270 (+4294877453), Inode bitmap at 286 (+4294877469)
  Inode table at 3008-3254 (+4294880191)
  8192 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 90113-98304
  Free inodes: 21737-23712
Group 12: (Blocks 98305-102399) [INODE_UNINIT, ITABLE_ZEROED]
  Checksum 0x6c11, unused inodes 1976
  Block bitmap at 271 (+4294869262), Inode bitmap at 287 (+4294869278)
  Inode table at 3255-3501 (+4294872246)
  4095 free blocks, 1976 free inodes, 0 directories, 1976 unused inodes
  Free blocks: 98305-102399
  Free inodes: 23713-25688
```