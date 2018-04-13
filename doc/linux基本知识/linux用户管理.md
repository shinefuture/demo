# 用户管理

## 1. 创建用户
### 1.1 命令
1. useradd [user]
  -u 指定uid
  -s 指定登录shell
  -c 添加用户注释
  -d 指定家目录，不自动创建，可以与-m连用，进行自动创建
  -e 账号终止日期，格式为MM/DD/YY
  -g 指定单个属组
  -G 指定多个属组，每个用户组用","逗号分隔
  -m 用户家目录不存在则自动建立
  -M 不建立用户家目录
  
2. passwd [user] 创建密码

3. 涉及文件
/etc/passwd
创建用户后，会自动在此文件创建一条相关信息。
格式为：用户名:密码(默认不显示，为x):uid:gid:comment:home_dir:shell_file
```
[root@basic ~]# tail -3 /etc/passwd
oldboy:x:501:501::/home/oldboy:/bin/bash
oldgirl:x:502:501::/home/oldgirl:/bin/bash
test:x:503:503::/home/test:/bin/bash
```

### 1.2 配置
/etc/skel/
在useradd创建用户的时候，会自动将/etc/skel/下的隐藏文件拷贝到新用户的家目录下
```
[root@basic ~]# ls /etc/skel/ -al
total 20
drwxr-xr-x.  2 root root 4096 Apr  1 20:25 .
drwxr-xr-x. 78 root root 4096 Apr  2 13:25 ..
-rw-r--r--.  1 root root   18 Mar 23  2017 .bash_logout
-rw-r--r--.  1 root root  176 Mar 23  2017 .bash_profile
-rw-r--r--.  1 root root  124 Mar 23  2017 .bashrc
```
作用：
1. 可以把通知的内容放到skel，让登录的人去看
2. 统一初始化新用户的环境变量
3. -bash-4.1$问题原因及解决方法（配置文件丢失，重新copy skel文件夹下到家目录下）

### 1.3 /etc/login.defs配置文件
/etc/login.defs文件是用来定义创建用户时需要的一些用户的配置信息。
如创建用户时，是否需要家目录，UID和GID的范围，用户及密码的有效期限等待。

### 1.4 /etc/default/useradd文件
/etc/default/useradd文件是在使用useradd添加用户时的一个需要调用的默认配置文件，可以使用"useradd -D 参数"，这样的命令格式来修改文件的内容。
```
[root@basic ~]# ls -l /etc/default/useradd 
-rw-------. 1 root root 119 Feb  9  2016 /etc/default/useradd
[root@basic ~]# cat  /etc/default/useradd 
# useradd defaults file
GROUP=100  //依赖于/etc/login.defs的USERGROUPS_ENAB参数，如果其参数为no，则此处控制
HOME=/home  //定义家目录创建于哪个目录下
INACTIVE=-1  //是否启用账号过期停权，-1表示不启用
EXPIRE=   //账号终止日期，不设置表示不启用
SHELL=/bin/bash  //定义shell的默认路径
SKEL=/etc/skel  //配置新用户家目录的默认文件存放路径，创建新用户时，默认文件从此路径拷贝到家目录
CREATE_MAIL_SPOOL=yes  //创建mail文件
```
useradd -D 参数：
  -b default_home 定义家目录所属目录
  -e default_expire_date 用户账号停止日期
  -f default_inactive 账号过期几日后停权
  -g default_group 属组
  -s default_shell 登入后使用的shell

### 1.5 usermod 修改用户信息
参数与useradd类似

## 2. 删除用户
userdel -r [user] // -r,删除家目录
  --stdin 非交互式设置密码，例
``echo "12345"| passwd --stdin oldboy``

## 3. 添加组
groupadd [group]
相关文件：
/etc/group 用户组相关文件
/etc/gshadow 用户组加密相关文件

参数：
-g 指定gid

## 4. 删除组
groupdel [group]


## 5.用户管理查询相关命令

- w   Show who is logged on and what they are doing.
- who  show who is logged on
- last, lastb - show listing of last logged in users
- lastlog - reports the most recent login of all users or of a given user
- id - print real and effective user and group IDs