# linux的目录

必须要掌握的重要目录
- /etc/sysconfig/network-scirpts/ifcfg-eth0
  配置网络地址及GW等，使得网卡生效
  1. 重启网卡/etc/init.d/network restart (影响所有网卡)
  2. ifdown eth0 && ifup eth0 ->只针对eth0网卡
  通过setup->"network configuration"修改网卡配置就是在改这个文件
- /etc/resolv.conf
  设置linux本地的**客户端DNS**的配置文件
  setup->"network configuration"->"DNS configuration"
  DNS配置小结：
  - linux客户端DNS可以在网卡配置文件里设置(ifcfg-eth0)
  - linux客户端DNS也可以在/etc/resolv.conf里设置
  - 网卡里的设置DNS优先于/etc/resolv.conf，如果重启网络,网卡的dns会覆盖/etc/resolv.conf的设置
- /etc/hosts
  设定用户IP与名字（域名）的对应解析表，相当于本地LAN局域网内的DNS
- /etc/sysconfig/network
  可修改机器名即网卡启动，网关等配置。
  永久更改主机名，需同时修改以下两项：
  1. vi /etc/sysconfig/network  -> 修改HOSTNAME项
  2. hostname 主机名

- /etc/fstab
  实现开机要挂载的文件系统的一个文件

- /etc/rc.local
  用于存放开机自启动程序命令的文件（chkconfig常用来管理yum/rpm安装的程序服务的开机启动）。
  自己开发的程序，习惯把启动命令放入/etc/rc.local，实现开机启动这些程序。
  开机时会把/etc/rc.local里的内容执行一遍。

- /etc/inittab
  设定系统启动时init进程系统的runlevel运行级别，及加载相关级别对应的启动文件设置。

- /etc/exports
  设定**NFS网络文件系统**用的配置文件。
- /etc/init.d
  用来存放通过yum或rpm工具安装的软件的默认启动程序的目录，如centos/redhat。
- /etc/profile
  系统全局环境变量永久生效的配资文件，定义别名以及PATH变量等。
  用户的环境变量： ~/.bash_profile，~/.bashrc

- /etc/profile.d
  加载系统登录程序的一个目录，命令或文件独立存在。
  以文件的形式存在（*.sh为扩展名），但要可执行。
  时机：登录后执行的脚本所在地。

- /etc/issue
  记录用户登录前显示的系统版本等信息
- /etc/motd
  登录后提示信息。

- /etc/bashrc
- /usr/local
  一般用来存放用户**自编译安装软件的存放目录**，一般是通过**源码包安装**的软件，如果没有特别指定安装目录的话，一般是安装在这个目录中。相当于C:\Program files

- /usr/src
  内核源码存放的目录。

- /var/log/messages
  系统信息默认日志文件，非常重要。按周自动轮循。
  dmseg命令可以查看系统故障信息。
  依赖于rsyglog服务开启。

- /var/log/secure
  记录登入系统存取信息的文件，按周自动轮循，如pop3、ssh、telnet、ftp等都会记录在此。
  系统安全的日志文件，依赖于rsyglog服务开启。

- /var/spool/cron/root
  定时任务crontab默认路径，按用户名命名的文件。

- /proc/cpuinfo
  处理器的信息
- /proc/meminfo
  系统内存信息
- /proc/loadavg
  系统负载平均值信息。
  负载值不要超过CPU的核数。看负载top，uptime。

- /proc/mounts
  设备的挂载信息，df -h类似。

其他：
- /usr 
  存放Unix系统商开发的程序。
- /usr/local
  存放用户自己安装的程序，一般编译软件的时候默认路径，yum或rpm包安装默认路径不再是这里了。
- /opt
  在某些系统，用于存放第三方厂商开发的程序。
- /home
  存放用户的数据
- /bin
  常用二进制命令所在的目录。比如ls、cp、mkdir、rm、cut等命令；/bin目录和/usr/bin类似。
- /boot 
  linux的内核及系统引导程序所需的文件目录。安装时，一般要分一个boot分区，常为128M。
- /dev
  设备文件的目录，如声卡、磁盘、光驱等。
  设备必须经过mount挂载，才可以在终端以目录方式访问。
- /etc 
  - 二进制软件安装包（yum,rpm）**配置文件**默认路径
  ```
  exports  ->NFS filesystem access control list(optional)
  fstab  ->Static information about filesystems(optional)
  hosts ->Static information about host names(optional)
  inittab ->configuration file for init(optional)
  issue  ->pre-login message adn identification file(optional)
  ld.so.conf ->list of extra dirctories to search for shared libaries(optional)
  motd ->post-login message of the day file(optional)
  networks  ->static information about network names(optional)
  passwd  -> the password file(optional)
  profile  -> systemwide initialization file for sh shell logins(optional)
  resolv.conf  -> resolver configuration(optional) : DNS客户端文件
  services ->port names for network services(optional)
  syslog.conf  -> configuration file for syslogd(optional)
  ```
  - 服务启动命令存放的目录/etc/init.d/(yum,rpm)。 (service sshd start)
- /lib
  库文件存放目录
- /root 
  root账户的家目录
- /tmp
  临时文件目录
- /var
  一般是日志文件，cache的目录
- /proc
  显示内核及进程信息的虚拟文件系统
- /sbin和/usr/sbin
  超级用户命令的目录
