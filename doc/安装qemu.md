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

## 2. 制作kernel镜像及根文件系统

### 2.1 制作kernel镜像

- 解压下载的kernel文件，进入解压后的文件夹，运行下面的指令编译:
```
export ARCH=arm  
export CROSS_COMPILE=arm-linux-gnueabi-  
make vexpress_defconfig  
make zImage -j8  
make modules -j8  
make dtbs  
```

- 随后就可以将生成的zImage和dtbs拷贝到单独的文件夹中，拷贝指令：
```
mkdir ../extra_folder -p 
cp arch/arm/boot/zImage ../extra_folder/  
cp arch/arm/boot/dts/*ca9.dtb ../extra_folder/  
cp .config  ../extra_folder/
```

### 2.2 制作根文件系统
根文件系统由基本的运行命令、库和字符设备构成。
基本的运行命令需要使用Busybox，这个库也需要进行交叉编译。

解压源代码，在源代码目录下运行：
```
make defconfig  
make menuconfig
# 进入menuconfig后，配置成静态编译
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -j8 
make install ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -j8
```
安装完成后，该目录下生成的_install，即是基本的运行指令。
然后就开始一步步建立根文件系统。

#### 2.2.1 创建rootfs目录
```
mkdir rootfs/lib -p
```

#### 2.2.2 拷贝busybox命令
```
cp _install/* -r ../rootfs/
```

#### 2.2.3 从工具链拷贝运行库到lib下
```
cp -P /usr/arm-linux-gnueabi/lib/* rootfs/lib/ 
```

#### 2.2.4 创建4个tty终端设备
c代表字符设备，4是主设备号，1~2~3~4是次设备号
```
mknod rootfs/dev/tty1 c 4 1  
mknod rootfs/dev/tty2 c 4 2  
mknod rootfs/dev/tty3 c 4 3  
mknod rootfs/dev/tty4 c 4 4  
```

## 3. 启动qume模拟系统

- 使用下面的指令启动，会在当前窗口中显示qemu虚拟出的终端
```
qemu-system-arm -M vexpress-a9 -m 512M -dtb extra_folder/vexpress-v2p-ca9.dtb -kernel extra_folder/zImage -nographic -append "root=/dev/mmcblk0 rw console=ttyAMA0" -sd a9rootfs.ext3  
```

- 使用下面的指令启动，会另开一个窗口显示虚拟终端
```
qemu-system-arm -M vexpress-a9 -m 512M -dtb extra_folder/vexpress-v2p-ca9.dtb -kernel extra_folder/zImage -append "root=/dev/mmcblk0 rw" -sd a9rootfs.ext3 
```

```
qemu-system-arm -M vexpress-a9 \
-smp 4 \
-m 1024M \
-kernel /home/wxm/qemu/linux-4.15/arch/arm/boot/zImage \
-append "rdinit=/linuxrc console=ttyAMA0 loglevel=8" \
-dtb /home/wxm/qemu/linux-4.15/arch/arm/boot/dts/vexpress-v2p-ca9.dtb \
-nographic
```