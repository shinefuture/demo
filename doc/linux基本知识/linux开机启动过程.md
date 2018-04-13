# linux的开机启动过程

1. 开机BIOS自检
2. MBR引导
硬盘0柱面0磁道1扇区的前446byte
3. grub引导菜单
cat /etc/grub.conf
4. 加载内核kernel
5. 启动init进程
6. 读取inittab文件，执行rc.sysinit,rc等脚本
```
/etc/inittab
/etc/rc.d/sysinit  ->/etc/fstab
                   ->/etc/rc.d/rc.local
/etc/rc.d/rc3.d/ <==文本模式
```
7.启动mingetty,进入系统登录界面