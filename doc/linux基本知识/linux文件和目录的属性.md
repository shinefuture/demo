# linux文件和目录的属性及权限

## 1. 文件属性
- 查看文件的详细属性：
ls -li [file]
stat [file]

- 访问文件
  通过文件名->inode->blocks

- 查看磁盘分区时inode节点大小属性：
dumpe2fs /dev/sda2| grep -i "inode size"
```
[root@shine ~]# dumpe2fs /dev/sda1| grep -i "inode size" 
dumpe2fs 1.41.12 (17-May-2010)
Inode size:               128
[root@shine ~]# dumpe2fs /dev/sda3| grep -i "inode size" 
dumpe2fs 1.41.12 (17-May-2010)
Inode size:               256
```

- 查看文件系统inode总量以及剩余量：
```
[root@shine ~]# df -i
Filesystem      Inodes IUsed   IFree IUse% Mounted on
/dev/sda3      2346512 55383 2291129    3% /
tmpfs           125514     1  125513    1% /dev/shm
/dev/sda1        51200    39   51161    1% /boot
```

- 查看磁盘使用量
```
[root@shine ~]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        36G  1.5G   32G   5% /
tmpfs           491M     0  491M   0% /dev/shm
/dev/sda1       190M   40M  141M  22% /boot
```

- 如何生成及指定inode大小
  格式化命令：mkfs.ext4 -b 2048 -I 256 /dev/sdb

## 2. 文件权限

- file
  查看文件的类型
- last
  查看用户登录信息，/var/log/wtmp数据文件
- lastlog
  显示最近登录的用户信息，/var/log/lastlog数据文件


## 3. 硬链接与软链接
在linux系统中，链接分两种：一种被称为硬链接（Hard Link），另一种被称为符号链接或软链接（Symbolic link）
  - 默认不带参数情况下，ln命令创建的是硬链接，带-s参数的ln命令创建的是软链接；
  - 硬链接文件与源文件的inode节点号相同，而软链接文件的inode节点号与源文件不同；
  - ln命令不能对目录创建硬链接，但可以创建软链接，对目录的软链接会经常被用到；
  - 删除软链接文件，对源文件及硬链接文件无影响；
  - 删除文件的硬链接文件，对源文件及软链接文件无任何影响；
  - 删除链接文件的源文件，对硬链接文件无影响，会导致其软链接失效（红底白字闪烁状）；
  - 同时删除源文件及其硬链接文件，整个文件才会被真正的删除；
  - 软链接可以跨文件系统，硬链接不可以跨文件系统。

## 4. 文件属性之用户和用户组

### 4.1 UID和GID
- UID范围
  - 0
    超级用户
  - 1-499
    虚拟用户，保留给系统使用的UID。
  - 500-65535
    普通用户，使用useradd [user]建立的账户，默认从500开始。
  可以从/etc/passwd查询到UID及GID，或者使用命令id [user]
```
[wxm@shine ~]$ id wxm
uid=500(wxm) gid=500(wxm) 组=500(wxm)
[wxm@shine ~]$ id root
uid=0(root) gid=0(root) 组=0(root)
```

### 4.2 用户及用户组配置文件
linux系统下的账户文件主要有/etc/passwd、/etc/shadow、/etc/group、/etc/gshadow四个文件

和用户相关的文件：
  /etc/passwd 用户的配置文件
  /etc/shadow 用户影子口令文件

- /etc/passwd
  root   : x      :0       :0       :root    :/root   :/bin/bash

| root | x   | 0   | 0   | root | /root | /bin/bash |
| ---- | --- | --- | --- | -----| ----- | --------- |
|账号名称 | 账号密码 | 账号UID | 账号组GID | 用户说明 | 用户家目录 | shell解释器 |

### 4.3 linux的读、写、执行权限说明
#### 4.3.1 文件权限
rwx权限说明：
- 可读r : 表示具有读取、阅读**文件内容**的权限
- 可写w : 表示具有新增、修改**文件内容**的权限
  - 无r权限，但有w权限，vi编辑文件会提示无法编辑（但可强制编辑），echo可以重定向或追加
  - 特别提示：删除文件(修改文件名等)的权限是受父目录的权限控制，和文件本身权限无关
原因是：删除文件即是删除文件名，而文件名是存储在父目录的block里（即父目录的文件内容），并指向这个文件的inode（inode存储权限信息），因此删除文件的权限受父目录权限控制。

- 可执行x : 表示具有执行文件的权限
  - 文件本身要能够执行
  - 普通用户同时还需要具备r的权限才能执行
  - root用户只要有x的权限就能执行

#### 4.3.2 目录权限
rwx权限说明：
- 可读r : 表示具有浏览目录下面文件及子目录的权限，即ls dir
  - 如果没有x权限，不能进入到目录里，即无法cd dir
  - 如果没有x权限，ls列表时可以看到所有文件名，但是会提示无权访问目录下的文件
  - 如果没有x权限，ls -l列表，所有的属性会带有问号，也会提示无权访问目录下的文件，但可以看到所有文件名

- 可写w : 表示具有增加、删除或修改目录内**文件名**（一般指文件名）的权限（需要x权限配合）
- 可执行x : 表示具有**进入目录**的权限，例如:cd dir
  但是没有r就无法列表文件及目录，没有w就无法新建和删除。

### 4.4 权限相关命令
#### 4.4.1 chmod
- chmod数字权限
r: 4
w: 2
x: 1
-: 0
案例：
rwxrwxrwx  777
rw-rw-rw-  666
rw-r-xr-x  655

- chmod字符权限
![chmod字符权限](https://i.imgur.com/O2OfqON.png)
案例：
```
chmod u-x test.sh
chmod g+w test.sh
chmod u-x,g+w,o-rwx test.sh
chmod ugo=rw test.sh
chmod a=rw test.sh
```

- 对于目录的权限，要用到-R参数，递归子目录及文件。


#### 4.4.2 默认权限分配命令，umask
1. 比较安全的权限：
文件权限:rw-r--r-- 644
目录权限:rwxr-xr-x 755

2. 默认权限计算方法
```
root:
[root@basic wxm]# umask
0022
user:
[wxm@basic ~]$ umask
0002
```
初始权限去除umask对应的权限，即创建文件或目录时的默认权限；
文件的初始权限为666;
目录的初始权限为777;
root用户，umask为0022，即group和other去除w权限，因此文件默认权限为666-022=644，目录默认权限为777-022=755;
一般用户，umask为0002，即other用户去除w权限，因此文件默认权限为666-002=664，目录默认权限为777-002=775；

3. umask修改
 /etc/profile文件中

#### 4.4.3 suid，针对命令和二进制程序
1. 用户或属主对应的前三位权限的x位上如果有s就表示suid权限。
  当x位上没有小写x执行权限的时候，suid的权限显示的就是大S。
2. suid作用是让普通用户可以以root(或其他)的用户角色运行只有root(或其他)账号才能运行的程序或命令，或程序命令对应本来没有权限操作的文件等。
  注意：su及sudo的区别，suid为某一个命令设置特殊权限（使用者为所有人）。
3. setuid设置方法：chmod u+s [cmdfile]或chmod 4755 [cmdfile] //前面添加4
  查此类命令二进制文件：find / -type f -perm 4755 -exec ls -l {} \;
```
[root@basic ~]# find / -type f -perm 4755 -exec ls -l {} \;
-rwsr-xr-x. 1 root root 14368 Mar 17  2015 /usr/libexec/polkit-1/polkit-agent-helper-1
-rwsr-xr-x. 1 abrt abrt 10296 Mar 23  2017 /usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
-rwsr-xr-x. 1 root root 257824 Sep  1  2017 /usr/libexec/openssh/ssh-keysign
-rwsr-xr-x. 1 root root 54464 Mar 22  2017 /usr/bin/at
-rwsr-xr-x. 1 root root 75640 May 11  2016 /usr/bin/gpasswd
-rwsr-xr-x. 1 root root 51784 Aug 24  2016 /usr/bin/crontab
-rwsr-xr-x. 1 root root 30768 Nov 24  2015 /usr/bin/passwd
-rwsr-xr-x. 1 root root 70480 May 11  2016 /usr/bin/chage
-rwsr-xr-x. 1 root root 40240 May 11  2016 /usr/bin/newgrp
-rwsr-xr-x. 1 root root 22544 Mar 17  2015 /usr/bin/pkexec
-rwsr-xr-x. 1 root root 9000 Oct  4 10:26 /usr/sbin/usernetctl
-rwsr-xr-x. 1 root root 53480 Jan 26 15:41 /bin/umount
-rwsr-xr-x. 1 root root 77560 Jan 26 15:41 /bin/mount
-rwsr-xr-x. 1 root root 36488 Mar 22  2017 /bin/ping6
-rwsr-xr-x. 1 root root 34904 Mar 23  2017 /bin/su
-rwsr-xr-x. 1 root root 38520 Mar 22  2017 /bin/ping
-rwsr-xr-x. 1 root root 10272 Mar 22  2017 /sbin/pam_timestamp_check
-rwsr-xr-x. 1 root root 34840 Mar 22  2017 /sbin/unix_chkpwd
```

#### 4.4.4 sgid
与suid不同的是，sgid既可以针对文件也可以针对目录设置；
sgid是针对用户组权限位的。
setgid设置方法： chmod g+s [cmd_file/dir] 或chmod 2755 [cmd_file/dir] //前面增加2

1. 对于文件，sgid的功能如下：
  - sgid仅对二进制命令程序有效
  - 二进制命令或程序需要有可执行权限x
  - 执行命令的任意用户可以获得该命令程序执行期间**所属组**的权限
```
[root@basic ~]# ls -l `which locate`
-rwx--s--x. 1 root slocate 38464 Mar 12  2015 /usr/bin/locate
```
2. 对于目录，sgid的功能如下：
  - linux里默认情况所有用户创建文件，默认用户和组都是自身
  - sgid可以让用户在此目录下创建的文件和目录，具有和此目录相同的用户组设置。

setgid位主要用在目录中，当位某个目录设置了setgid位以后，在该目录中新创建的文件具有该目录的所属组权限，而不是创建该文件的用户的默认所有者。这样，使得在多个用户之间共享一个目录中的文件变得简单。
提示：用八进制数2000表示setgid权限位。


#### 4.4.5 粘贴位 
一个目录即使它的所有权限都开放rwxrwxrwx，如果设置了粘贴位，除非**目录的属主**和**root用户**有权限删除它，除此之外其它用户都不能删除这个目录，例/tmp目录。

比sgid的更安全措施，让多个用户都具有写权限的目录，每个用户只能删自己的文件。表现在other位，用t表示；
如果没有执行权限x，就是T；如果有执行权限x，就是t表示。

设置方法：chmod o+t /tmp或chmod 1777 /tmp  //增加数字1
```
[root@basic ~]# ls -dlhi /tmp
390146 drwxrwxrwt. 3 root root 4.0K Apr  2 13:13 /tmp
```

## 5. 用户和组管理
``chown [选项]... [所有者][:[组]] 文件...``
方法：
- chown [用户] [文件或目录]  // 仅仅授权用户
- chown :[组] [文件或目录]   // 仅仅授权组，等同于"chgrp [组] [文件或目录]"
- chown [用户]:[组] [文件或目录] // 同时授权用户和组

强调：
1. 其中的冒号":"可以用点号"."替代
2. 要授权的用户和组名，必须使linux系统里实际存在的。

