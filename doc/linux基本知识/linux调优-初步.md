# linux调优

## 1. 关闭SELinux功能
```
cat /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
enforcing ： 开启
disable : 关闭

getenforce : 查看selinux状态
setenforce : 用于命令行管理SElinux的级别，后面数字表示设置对应的级别。
提示：修改配置SELinux后，要想使其生效，必须要重启系统。因此，可配合使用setenforce 0这个临时使其关闭的命令，这样在重启前后都可以使得SELinux关闭生效了
```
[root@shine ~]# sed 's#SELINUX=enforcing#SELINUX=disabled#g' /etc/selinux/config -i
[root@shine ~]# getenforce 
Enforcing
[root@shine ~]# setenforce 
usage:  setenforce [ Enforcing | Permissive | 1 | 0 ]
[root@shine ~]# setenforce 0
[root@shine ~]# getenforce 
Permissive
```
数字0表示Permissive，即给出警告提示，但不会阻止操作，相当于disabled。
数字1表示Enforcing，即表示SELinux为开启状态。


## 2. 设定运行级别为3 （文本模式）
设定运行级别（runlevel）为3，即表示使用文本命令行模式管理Linux
```
[root@shine ~]# cat /etc/inittab
# inittab is only used by upstart for the default runlevel.
#
# ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
#
# System initialization is started by /etc/init/rcS.conf
#
# Individual runlevels are started by /etc/init/rc.conf
#
# Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
#
# Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
# with configuration in /etc/sysconfig/init.
#
# For information on how to write upstart event handlers, or how
# upstart works, see init(5), init(8), and initctl(8).
#
# Default runlevel. The runlevels used are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
# 
id:3:initdefault:
```
查看运行级别：
```
[root@shine ~]# runlevel
N 3
```
切换运行级别：
```
init [runlevel]
```

## 3. 精简开机系统启动


## 4. 关闭防火墙
临时关闭：
```
[root@shine ~]# /etc/init.d/iptables stop
iptables: Setting chains to policy ACCEPT: filter          [  OK  ]
iptables: Flushing firewall rules:                         [  OK  ]
iptables: Unloading modules:                               [  OK  ]
```
查看状态：
```
[root@shine ~]# /etc/init.d/iptables status
iptables: Firewall is not running.
```
开启也不启动：
```
[root@shine ~]# chkconfig iptables off
```

## 5. 中文显示设置
查看配置
```
[root@shine ~]# cat /etc/sysconfig/i18n
LANG="en_US.UTF-8"
SYSFONT="latarcyrheb-sun16"
```
修改配置：
```
[root@shine ~]#  echo 'LANG="zh_CN.UTF-8"' > /etc/sysconfig/i18n 
[root@shine ~]# cat /etc/sysconfig/i18n                          
LANG="zh_CN.UTF-8"
```
使配置生效：
```
[root@shine ~]# echo $LANG
en_US.UTF-8
[root@shine ~]# source /etc/sysconfig/i18n 
[root@shine ~]# echo $LANG            
zh_CN.UTF-8
```

## 6. 历史记录数即登录超时环境变量设置
1. 设置闲置账号超时时间
临时生效：
```
export TMOUT=10
```
2. 设置命令行历史记录数
  - 设置历史记录最大数量：
``export HISTSIZE=5``
设置后使用history只记录最后5条。
  - 设置历史记录文件.bash_history最大保存条目：
```
export HISTFILESIZE=5
cat ~/.bash_history
```
3. 永久生效：
```
echo 'export TMOUT=300' >> /etc/profile
echo 'export HISTSIZE=5' >> /etc/profile
echo 'HISTFILESIZE=5' >> /etc/profile
source /etc/profile
```

## 7. 隐藏linux登录版本信息
```
[root@shine wxm]# cat /etc/issue
CentOS release 6.9 (Final)
Kernel \r on an \m
[root@shine wxm]# cat /etc/issue.net 
CentOS release 6.9 (Final)
Kernel \r on an \m
```
将其清空：
```
[root@shine wxm]# > /etc/issue
[root@shine wxm]# > /etc/issue.net
```