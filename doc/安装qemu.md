# ubuntu 16.04 安装 QEMU记录

环境配置： ubuntu 16.04, kernel 4.15, busybox 1.28.1

参考链接：
[本文参考链接](http://blog.csdn.net/nxcxl88/article/details/53244754)
[参考一](http://blog.csdn.net/linyt/article/details/42504975)
[参考二](http://www.cnblogs.com/pengdonglin137/p/5023342.html)
[推荐](https://learningfromyoublog.wordpress.com/2016/04/05/131/)

## 1. 环境准备

### 1.1 kernel源码

有两种方式：git和wget，这里使用wget下载方式。从kernel.org直接下载很慢（几KB），因此可以借用国内的代理（可以达到1~2M）：

```
从kernel下载：
wget https://www.kernel.org/pub/linux/kernel/v4.x/linux-4.15.tar.xz
使用代理：
https://mirror.tuna.tsinghua.edu.cn/kernel/v4.x/linux-4.15.tar.xz
```
可以去[kernel镜像网站](https://mirror.tuna.tsinghua.edu.cn/kernel/v4.x/)找最新的版本。

### 1.2 busybox : 用于生成根文件系统

```
wget http://www.busybox.net/downloads/busybox-1.28.1.tar.bz2
```
可以去[busybox下载网站](https://busybox.net/downloads/)找最新的版本。

### 1.3 安装arm交叉编译链

arm交叉编译链有很多种，有的书上装了arm-linux-gnueabi 和 arm-linux-gnueabihf两种，安装方法如下：
```
sudo apt-get install gcc-arm-linux-gnueabi
```

### 1.4 安装qemu模拟器
```
sudo apt-get install qemu
```

## 2. 制作根文件系统及kernel镜像

### 2.1 制作根文件系统
根文件系统由基本的运行命令、库和字符设备构成。
基本的运行命令需要使用Busybox，这个库也需要进行交叉编译。

#### 2.1.1 编译
- 解压源代码，在源代码目录下运行：
```
make defconfig  
export ARCH=arm  
export CROSS_COMPILE=arm-linux-gnueabi-
make menuconfig
```

- 进入menuconfig后，配置成静态编译:
```
   Settings  ---> 
     --- Build Options 
     [*] Build static binary (no shared libs) 
```
- 最后进行编译：
```
make install -j8
```
安装完成后，该目录下生成的_install，即是基本的运行指令。

##### 2.1.2 添加根文件目录其它文件

然后就开始一步步建立根文件系统。

1. 创建rootfs目录
```
mkdir rootfs/lib -p
cp busybox/_install/* rootfs/ -r
```
2. 创建etc、dev等目录
```
cd rootfs
mkdir {etc/init.d/,dev,mnt} -p
```

3. 在rootfs/ect/init.d目录下新创建一个rcS文件，并写入如下内容：
```
mkdir -p /proc
mkdir -p /tmp
mkdir -p /sys
mkdir -p /mnt
/bin/mount -a
mkdir -p /dev/pts
mount -t devpts devpts /dev/pts
echo /sbin/mdev > /proc/sys/kernel/hotplug
mdev -s
```
需要修改rcS可执行权限：
```
chmod +x rootfs/etc/init.d/rcS
```

4. 在rootfs/etc目录下新创建一个fstab文件，并写入如下内容：
```
proc /proc proc defaults 0 0
tmpfs /tmp tmpfs defaults 0 0
sysfs /sys sysfs defaults 0 0
tmpfs /dev tmpfs defaults 0 0
debugfs /sys/kernel/debug debugfs defaults 0 0
```

5. 在rootfs/etc目录下新创建一个inittab文件，并写入如下内容：
```
::sysinit:/etc/init.d/rcS
::respawn:-/bin/sh
::askfirst:-/bin/sh
::ctrlaltdel:/bin/umount -a -r
```

6. 在rootfs/dev目录下创建如下设备节点，需要root权限：
```
cd rootfs/dev
sudo mknod console c 5 1
sudo mknod null c 1 3
```

7. 从工具链拷贝运行库到lib下
```
cp -p /usr/arm-linux-gnueabi/lib/* rootfs/lib/ 
```

### 2.2 制作kernel镜像

- 解压下载的kernel文件，进入解压后的文件夹，运行下面的指令编译:
```
export ARCH=arm  
export CROSS_COMPILE=arm-linux-gnueabi-  
make vexpress_defconfig  
```
- 配置initramfs，在initramfs source file中填入rootfs(最好绝对路径)，并把Default kernel command string清空： 
```
    General setup  --->
       [*] Initial RAM filesystem and RAM disk (initramfs/initrd) support
       (rootfs) Initramfs source file(s) 

    Boot options  --->
       () Default kernel command string
```
遇到的坑：使用SecureCRT不能使用退格键删除，与SecureCRT配置有关，配置如下：
```
选项->会话选项->终端->仿真->映射键，勾选“Backspace发送delete(B)”选项即可。
```

- 配置memory split为“3G/1G user/kernel split”，并打开高端内存：
```
    Kernel Features  --->
        Memory split (2G/2G user/kernel split)  --->
            (X) 3G/1G user/kernel split
```

- 开始编译kernel
```
make bzImage -j8 ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-
make dtbs
```

## 3. 启动qume模拟系统
```
qemu-system-arm -M vexpress-a9 \
-smp 4 \
-m 1024M \
-kernel linux/arch/arm/boot/zImage \
-append "rdinit=/linuxrc console=ttyAMA0 loglevel=8" \
-dtb linux/arch/arm/boot/dts/vexpress-v2p-ca9.dtb \
-nographic
```
注意：-dtb以及-kernel路径最好为绝对路径。