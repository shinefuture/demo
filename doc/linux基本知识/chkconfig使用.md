# chkconfig命令作用，使用
作用：
设置开机启动脚本

常用命令：
chkconfig --list | grep sshd ->查看是否有对应服务执行脚本
```
[root@shine ~]# chkconfig --list | grep ssh
sshd            0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
```
chkconfig --add sshd  ->添加开机启动进程，添加后默认直接开启
chkconfig sshd off  -> 取消开机启动
```
[root@shine ~]# chkconfig sshd off          
[root@shine ~]# chkconfig --list | grep ssh 
sshd            0:关闭  1:关闭  2:关闭  3:关闭  4:关闭  5:关闭  6:关闭
```
chkconfig sshd on  -> 设置为开机启动
```
[root@shine ~]# chkconfig --list | grep ssh
sshd            0:关闭  1:关闭  2:启用  3:启用  4:启用  5:启用  6:关闭
```

涉及关联路径：
/etc/init.d/  -> 所有要开机启动脚本的存放目录
/etc/rc3.d/   -> runlevel 3开启要启动的脚本目录

# chkconfig原理

1. 开机启动脚本书写规范：
```
[root@shine ~]# cat /etc/init.d/sshd | head
#!/bin/bash
#
# sshd          Start up the OpenSSH server daemon
#
# chkconfig: 2345 55 25
# description: SSH is a protocol for secure remote shell access. \
#              This service starts up the OpenSSH server daemon.
```
  chkconfig 2345 55 25 : 2345代表可执行文件sshd可以在runlevel 2/3/4/5上执行，55在启动时会被命名为S55sshd，25在取消启动时会被命名为K25sshd，数字越小代表执行顺序越靠前。
  必须有开头文件chkconfig以及description。
2. 开机脚本要放入/etc/init.d/目录下，以待chkconfig添加时可以查找到；
3. chkconfig --add sshd 
  chkconfig会自动去/etc/init.d/目录下查找sshd文件，在/etc/rc3.d/下，建立软连接，并命名为S55sshd：
```
[root@shine ~]# ls /etc/rc3.d/ -l | grep ssh
lrwxrwxrwx. 1 root root 14 3月  27 08:02 S55sshd -> ../init.d/sshd
```