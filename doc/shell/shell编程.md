# shell编程
## 1.Shell脚本的执行
### 1.1 加载时检查系统环境变量
当shell脚本执行时，会先查找系统环境变量ENV，该变量指定了环境文件（按顺序加载）：
/etc/profile、~/.bash_profile、~/.bashrc 、/etc/bashrc等
加载上述环境变量文件后，即开始执行脚本中的内容。

### 1.2 脚本执行方式
通常情况下，在执行shell脚本时，会向系统内核请求启动一个新的进程，以便在该进程中执行脚本的命令及子shell脚本。
执行方式：
- bash script-name 或 sh script-name
- path/script-name 或 ./scipt-name
  要求，脚本文件必须有可执行权限
- source script-name 或 . srcipt-name
  source或. 命令的功能是：在当前shell中执行source或.加载并执行，而不是产生一个子shell来执行。
  脚本中的变量相当于export到当前shell空间中。
  注意：.和后面的脚本名之间要有空格

```
oldboy@server:~/test$ echo $user

oldboy@server:~/test$ cat test.sh 
#!/bin/bash
user=`whoami`
oldboy@server:~/test$ ./test.sh 
oldboy@server:~/test$ echo $user 

oldboy@server:~/test$ . test.sh 
oldboy@server:~/test$ echo $user 
oldboy
```
- sh < script-name 或 cat script-name | sh 

## 2. shell变量
变量可分为：环境变量（全局变量）和普通变量（局部变量）。
 - 环境变量可以在创建它们的shell及其派生出来的任意子进程shell中使用；
   环境变量又可以分为自定义环境变量和bash内置的环境变量
 - 普遍变量只能在创建它们的shell函数或shell脚本中使用。
### 2.1 环境变量
#### 2.1.1 查看环境变量
在查看设置的变量时，有3个命令可以显示变量的值：set、env和declare
set : 输出所有的变量，包括全局变量和局部变量
env : 只显示全局变量
declare : 输出所有的变量、函数、整数和已经导出的变量。

set -o命令显示bash shell的所有参数配置信息
```
wxm@server:~$ set | head
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_COMPAT_DIR=/etc/bash_completion.d
BASH_LINENO=()
BASH_REMATCH=()
BASH_SOURCE=()
-bash: set: 写错误: 断开的管道
wxm@server:~$ env | tail
SHLVL=1
HOME=/home/wxm
LANGUAGE=zh_CN:zh
LOGNAME=wxm
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
SSH_CONNECTION=192.168.100.102 50710 192.168.100.121 22
LESSOPEN=| /usr/bin/lesspipe %s
XDG_RUNTIME_DIR=/run/user/1000
LESSCLOSE=/usr/bin/lesspipe %s %s
_=/usr/bin/env
wxm@server:~$ set -o |head
allexport       off
braceexpand     on
emacs           on
errexit         off
errtrace        off
functrace       off
hashall         on
histexpand      on
history         on
ignoreeof       off
```
#### 2.1.2 自定义环境变量
1. 设置环境变量
使用export或者declare进行设置：
  - export 变量名=value
  - 变量名=value ; export 变量名
  - declare -x 变量名=value
环境变量永久生效的常用配置文件：
  - 用户的环境变量配置
  家目录下的~/.bashrc，以及~/.bash_profile，推荐优先在~/.bashrc中进行设置
  - 全局环境变量配置
  /etc/profile、/etc/bashrc、/etc/profile.d/
  推荐优先在/etc/bashrc中进行设置
2. 设置登录提示的两种方式
  - 在/etc/motd中增加提示字符串
  - 在/etc/profile.d/增加echo脚本，登录时会自动将此目录下所有脚本执行一遍。

#### 2.1.3 显示与取消环境变量
1. 通过echo或printf命令打印环境变量
```
wxm@server:~$ echo $HOME
/home/wxm
wxm@server:~$ echo $UID
1000
wxm@server:~$ echo $PWD
/home/wxm
wxm@server:~$ echo $USER
wxm
wxm@server:~$ printf "$HOME\n"
/home/wxm
```
2. 用env或set显示默认的环境变量
```
wxm@server:~$ set | head
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:complete_fullquote:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_COMPAT_DIR=/etc/bash_completion.d
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="3" [2]="48" [3]="1" [4]="release" [5]="x86_64-pc-linux-gnu")
-bash: set: 写错误: 断开的管道
wxm@server:~$ env | tail
SHLVL=1
HOME=/home/wxm
LANGUAGE=zh_CN:zh
LOGNAME=wxm
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
SSH_CONNECTION=192.168.100.102 49254 192.168.100.121 22
LESSOPEN=| /usr/bin/lesspipe %s
XDG_RUNTIME_DIR=/run/user/1000
LESSCLOSE=/usr/bin/lesspipe %s %s
_=/usr/bin/env
```
3. 用unset取消本地变量和环境变量
```
wxm@server:~$ echo $HOME
/home/wxm
wxm@server:~$ unset HOME
wxm@server:/home/wxm$ echo $HOME
  //<-空
wxm@server:/home/wxm$
```
注意，unset只在当前shell及子shell起作用，父目录中对应的变量不变。

#### 2.1.4 环境变量初始化与对应文件生效顺序
系统运行shell的方式：
- 通过系统用户登录后默认运行的shell
- 非登录交互式运行shell
- 执行脚本运行非交互式shell

1. 登录默认shell，执行的配置文件
  - 首先加载/etc/profile，此文件会执行/etc/profile.d目录下所有脚本文件
  - 然后运行~/.bash_profile(ubunt是~/.profile)，这个文件会判断~/.bashrc文件是否存在，存在则执行，并设置用户环境变量PATH

2. 非登录交互式运行shell，执行的配置文件
用户的shell不是登录时启动的，如手动敲下bash时，这种方式只会加载~/.bashrc配置文件

3. 执行脚本运行非交互式shell，执行的配置文件


### 2.2 普通变量(本地变量)
普通变量只在当前shell生存期的脚本中使用。
#### 2.2.1 普通变量定义
1. 有以下3中写法：
  - 变量名=value
  值里有变量的会被解析后再输出。
  当内容为简单连续的数字、字符串、路径名时，可以这样使用。
  - 变量名='value'
  单引号里有什么就输出什么，所见即所得，即使内容有变量和命令，也不会执行，而是原样输出。
  只适合于纯字符串。
  - 变量名="value"
  输出变量内容时，引号里的变量及命令会经过解析后再输出内容。
  适合于字符串中附带有变量及命令且想将其解析后再输出的变量定义。
  - 总结：
  数字不加引号，其他默认加双引号。
  当变量名后面连接有其他字符时，必须给变量加上大括号{}，如$dbname_tname需要改成${dbname}_tname。

2. 把一个命令的结果作为变量的内容赋值方法
  - 变量名=\`ls\` #<==把命令用反引号引起来
  - 变量名=$(ls)  #<==把命令用$()引起来，推荐此种方法

## 3. shell中特殊变量
``man bash``，搜索关键字"Special Parameters"
### 3.1 特殊位置参数变量
特殊位置参数变量：
  - $0   获取当前执行的Shell脚本的文件名，如果执行脚本包含了路径，那么就包括脚本路径
  - $n   获取当前执行的shell脚本的第n个参数，如果n大于9，则用大括号括起来，如${10}
  - $#   获取当前执行的shell脚本后面接的参数的总个数
  - $*   获取当前shell脚本所有传参的参数，如果加上双引号，即"$*"，则表示将所有的参数视为单个字符串，相当于"$1 $2 $3"
  - $@   获取当前shell脚本所有传参的参数，如果加上双引号，即"$@"，则表示将所有的参数视为不同的独立字符串，相当于"$1" "$2" "$3" "..."；这是将多参数传递给其他程序的最佳方式。
注意：$*与$@不加引号时，作用相同；加上引号时，作用不同。

### 3.2 特殊状态变量
特殊状态变量：
  - $?  获取执行上一个命令的执行状态返回值(0为成功，非零为失败)
  - $$  获取当前执行的shell脚本的进程号(PID)
  - $!  获取上一个在后台工作的进程的进程号
  - $_  获取在此之前执行的命令或脚本的最后一个参数
1. $?返回值参考：
 0 表示运行成功
 2 权限拒绝
 1~125 表示运行失败，脚本命令、系统命令错误或参数传递错误
 126 找到该命令了，但是无法执行
 127 未找到要运行的命令
 大于128 命令被系统强制结束


## 4. bash shell内置变量命令
常用的有echo、eval、exec、export、readonly、read、shift、wait、exit和点(.)。
最常用的：echo、export、read、exit

(1)echo
命令格式：echo args #<==可以使字符串和变量的组合

(2)eval
命令格式:eval args
功能：当shell程序执行到eval语句时，shell读入参数args，并把它们组合成一个新的命令，然后执行。
也就是将字符串形式的命令进行解析执行。

(3)exec
命令格式：exec命令参数
功能：exec命令能够在不创建新的子进程的前提下，转去执行指定的命令，当指定的命令执行完毕后，此进程终止。

(4)read
命令格式：read 变量名表
功能：从标准输入读取字符串等信息，传给shell程序内部定义的变量。

(5)shift
命令格式： shift-Shift positional parameters
功能：shift语句会按如下方式重新命名所有的位置参数变量，即$2成为$1，$3成为$2，以此类推，在程序中每使用一次shift语句，都会使所有的位置参数依次向左移动一个位置，并使位置参数$#减1，直到减到0为止。

## 5.shell变量子串
可以在执行man bash命令后，搜索"Parameter Expansion"查找相应内容：
```
${parameter}  返回变量$parameter的内容
${#parameter}  返回变量$parameter内容的长度(按字符)，也适用于特殊变量
${parameter:offset}  在变量${parameter}中，从位置offset之后开始提取子串到结尾，不包含第offset个字符，相当于删除前offset个字符
${parameer:offset:length}  在变量${parameter}中，从位置offset之后开始提取长度为length的子串，不包含第offset个字符
${parameter#word}  从变量${parameter}开头开始删除最短匹配的word子串
${parameter##word}  从变量${parameter}开头开始删除最长匹配的word子串
${parameter%word}  从变量${parameter}结尾开始删除最短匹配的word子串
${parameter%%word}  从变量${parameter}结尾开始删除最长匹配的word子串
${parameter/pattern/string}  使用string代替第一个匹配的pattern
${parameter//pattern/string}  使用string代替所有匹配的pattern
```
总结：#从开头，%从结尾

## 6.shell特殊扩展变量
```
${parameter:-word}  如果parameter的变量值为空或未赋值，则会返回word字符串的值，不改变这个变量的值
${parameter:=word}  如果parameter的变量值为空或未赋值，则设置这个变量值为word，并返回其值。位置变量和特殊变量不使用
${parameter:?word}  如果parameter的变量值为空或未赋值，那么word字符串将被作为标准错误输出，否则输出变量的值
${parameter:+word}  如果parameter的变量值为空或未赋值，则什么也不做，否则word字符串将替换变量的值
```
说明：每个表达式的冒号都是可选的，如果省略了表达式的冒号，则将每个定义中断"为空或未赋值"部分改为"未赋值"，即冒号用来测试变量是否为空，后一个运算符用来测试是否未赋值。








## 1.常用表达式
``man test``
在[]以及在test中使用。
### 1.1 文件表达式：
常用文件测试操作符
-f 文件，英文file      文件存在且为普通文件则真，即测试表达式成立
-d 文件，英文directory 文件存在且为目录则真，即测试表达式成立
-s 文件，英文size      文件存在且文件大小不为0则真，即测试表达式成立
-e 文件，英文exist     文件存在则真，即测试表达式成立

-r 文件，英文read      文件存在且可读则真
-w 文件，write        文件存在且可读则真
-x 文件，excutable    文件存在且可执行则真
-L 文件，Link         文件存在且为链接文件则真
f1 -nt f2, newer than  文件f1比文件f2新则真
f1 -ot f2, older than  文件f1比文件f2旧则真

例：文件存在则echo 1，否则echo 0 
``[ -f oldboy ]&&echo 1 || echo 0``
最好加上双引号，否则结果肯能不太准：
``[ -f "oldboy" ]&&echo 1 || echo 0``

### 1.2 字符串表达式
字符串测试操作符：
-z "字符串"，zero      若串长度为0则真
-n "字符串"，no zero   若串长度不为0则真
"串1" = "串2"           若串1等于串2则真，可使用"=="代替"="
"串1" != "串2"          若串1不等于串2则真，不能使用"!=="代替"!="
注意：
1.以上表格中的字符串测试操作符号务必要用""引起来
2.比较符号两端有空格。

例：
``[ -n "abc" ]&& echo 1 || echo 0``

### 1.3 整数二元比较操作符
| 在[]及test中使用 | 在(())和[[]]中使用 | 说明 |
| --- | --- | --- |
| -eq | ==或= | equal的缩写，相等 |
| -ne | != | not equal的缩写，不相等 |
| -gt | > | 大于 greater than |
| -ge | >= | 大于等于 greater equal |
| -lt | < | 小于less than |
| -le | <= | 小于等于less equal |

提示：
"="和"!="在[]中使用不需要转义，包含">"和"<"的符号在[]使用需要转义，对于数字不转义的结果未必会报错，但是结果可能不会对。
例：``if[["$a" \< "$b"]]``

### 1.4 逻辑操作符
| 在[]及test中使用 | 在[[]]中使用 | 说明 |
| --- | --- | --- |
| -a | && | and，与 |
| -o | 双竖线 | or，或 |
| ! | ! | not，非 |
例：
``[ -f $f1 ] && [ -f $f2 ] && echo 1 || echo 0``等价于``[ -f $f1 -a -f $f2 ] && echo 1 || echo 0``
或者``[[ -f $f1 && -f $f2 ]] && echo 1 || echo 0``

 