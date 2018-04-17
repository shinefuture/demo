# shell脚本的建立和执行

## 1. 脚本的建立

脚本文件开头：

```bash
#!/bin/bash
# 或者以下方式
#!/bin/sh #<==255个字符以内
```

## 2. 脚本的执行

通常情况下，在执行shell脚本时，会向系统内核请求启动一个新的进程，以便在该进程中执行脚本的命令及子shell脚本。  

加载的环境变量：  
- 开机登录时，会按序加载/etc/profile，HOME/.bash_profile，HOME/.bashrc中，若/etc/bashrc存在，会继续加载/etc/bashrc
- 使用bash命令进入子shell时，会加载HOME/.bashrc

执行方式：
- bash script-name 或 sh script-name  
  用于脚本文件本身没有可执行权限的情况。  
- path/script-name 或 ./scipt-name  
  要求，脚本文件必须有可执行权限。  
  加权限方法：chmod +x script-name  
- source script-name 或 . srcipt-name  
  source或. 命令的功能是：在当前shell中执行source或.加载并执行，而不是产生一个子shell来执行。  
  脚本中的变量相当于export到当前shell空间中。    
  注意：.和后面的脚本名之间要有空格。  
- sh<script-name 或 cat script-name |sh  
  将所有字符串拼接成命令的形式，然后经由管道或重定向给bash操作。


