# VMware使用

# 新设备设置网卡步骤
setup
设置eth0->save
ifup eth0

# clone
解决clone后网卡不能用：
1. 编辑eth0的配置文件：vim /etc/sysconfig/network-scripts/ifcfg-eth0,删除HWADDR地址那一行及UUID的行，如下：
```
HWADDR=00:0c:29:44:d4:f9
UUID=3b88b775-fc29-4b5e-8ac8-b3eed9ba36e1
```
2. 如果有必要再清空如下文件：
  `` > /etc/udev/rules.d/70-persistent-net.rules``

3. 重启

# Centos6 yum源更新
1. 备份
```
cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d//CentOS-Base.repo.ori
```
2. 下载
```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo
```
3. 清理
```
 yum clean all
```
4. 更新缓存
```
yum makecache
yum install tree -y
yum update -y
```

5. 安装软件包
```
yum install tree telnet dos2unix sysstat lrzsz nc nmap -y
```