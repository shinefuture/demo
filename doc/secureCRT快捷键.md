# secure CRT常用快捷键

- 挂起 : ctrl+s
- 解除挂起 : ctrl+q
- 退出当前用户登录： ctrl+d

命令行光标移动：
- ctrl+a 移动到命令行前
- ctrl+e 移到到命令行尾
- ctrl+u 清除当前行
- ctrl+k 删除当前光标至行尾的字符


- ctrl+c 中断操作
- ctrl+shift+c 拷贝
- ctrl+shift+v 黏贴
- ctrl+l 清屏
- ctrl+r 命令行查找
- !609 运行历史第609命令
- !w 运行最近的w开头的命令

文件传输设置：
ubuntu需要安装：sudo apt-get install lrzsz 
centos需要安装：yum install lrzsz -y

传输命令： 
rz 上传文件到远端
sz 从远端下载文件

注意事项：
不能是目录，如果是目录需要打包，最好打成zip包
不要勾选“以ASCII方式上传文件”选项