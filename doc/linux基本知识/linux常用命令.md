# 1. linux常用命令

- ls [文件或目录]
  -d 查看目录

- touch [文件名]
  如果文件不存在，就建立新文件，如果存在，就改变文件的访问时间atime等时间戳信息。
  touch abc{1..100}，创建abc1,abc2,..,abc100等共100个文件。

- echo

- cat "text" > text.txt # >重定向，>>追加重定向
特殊符号：
```
>或1>  输出重定向：把前面输出的内容输入到后边的文件中，会清除文件原有内容
>>或1>> 追加重定向：把前面输出的内容追加到后边的文件中，不会清除原有内容
0<或<  输入重定向：输入重定向用于改变命令的输入，后面指定输入内容，前面跟文件名
0<<或<< 追加输入重定向：后跟字符串，用来表示“输入结束”，也可用ctrl+d来结束输入
2> 错误重定向：把错误信息输入到后边的文件中，会删除文件原有内容
2>> 错误追加重定向：把错误信息追加到后边的文件中，不会删除文件原有内容
```
  - 案例1：
```
#cat >> abc.txt 0<<EOF
> I am studying linux.
> EOF
#cat abc.txt
```
结果是：I am studying linux.
  - 案例2：
``# cho abc 1>a.txt 2>&1``
将正确输出放入a.txt中，将错误信息放入正确输出的文件中，2追随1
``# cho abc 2>b.txt 1>&2``
将错误信息放入b.txt中，将正确输出放入错误信息的文件中，1追随2
  - 案例3：
``# cho abc &>a.txt``
与案例2效果相同，正确与错误信息均放入a.txt中。

- xargs
  xargs是给命令 传递参数 的一个过滤器
  -n 数字，分组

- cp [-aifpru] [源文件] [目标文件]
  -a :相当于-pdr
  -d :若源文件为链接文件，则复制链接文件属性而非档案本身；
  -f :强制，若目标档案已经存在且无法开启，则移除后再尝试；
  -i :若目标文件已经存在时，在覆盖时会先询问；
  -p :连同档案的属性一起复制过去，而非使用默认属性；
  -r :递归，用于复制目录；
  -u :若目标文件存在，则目标文件比源文件旧时才复制；
提示：如果源文件是多个，那么目标文件在最后，且是目录。
覆盖拷贝文件不提示overwrite信息的方法：
  - \cp /mnt/test.txt /tmp/test.txt
  - /bin/cp /mnt/test.txt /tmp/test.txt
  - 详细说明：
  cp命令默认是不会提示overwrite的，但是cp的-i选项会提示，而一般Linux的用户环境文件~/.bashrc中会把cp命名成alias cp='cp -i'，因此会有overwrite提示信息。

- alias
  可用命令alias显示当前环境中所有别名。例：
```
wxm@server:~/test$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias http_proxy='export http_proxy=http://127.0.0.1:8787/'
alias https_proxy='export https_proxy=http://127.0.0.1:8787/'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
```
  去除别名的命令，例：unalias ll，去除ll命令
  设置别名：alias ll='ls -alF'
  常用别名：
```
alias net='cat /etc/sysconfig/network-scripts/ifcfg-eth0'
```
  定义别名永久生效：
  - /etc/profile 全局生效
  - ~/.bashrc 当前用户生效

- mv [-ifu] [源文件/源目录] [目的文件/目的目录]
  -i :如果目的文件已存在，询问是否覆盖
  -f :强制执行，不会询问
  -u :若目的文件存在，则比源文件新，才会移动
提示：如果源文件是多个，那么目标文件在最后，且是目录。

- find 
  -type 文件类型(f(file),d(directory),c(character),b(block),s(socket),l(link))
  -name "文件名"
  -mtime 时间，+7:7天以前，7:第七天，-7:最近7天 
  ！ 取反
  -a and 与，交集
  -o or 或，并集
  - find 与 xargs搭配使用：
  find . -type f -name "*.log" |xargs rm -f
  将查找到的信息通过xargs组成一行传递给rm
  - find 与 -exec搭配使用
  find . -type f -name "*.log" -exec rm {};

- grep 
  -v 排除内容
  -B 除了显示匹配的一行之外，并显示改行之前的num行
  -A 除了显示匹配的一行之外，并显示改行之后的num行
  -C 除了显示匹配的一行之外，并显示改行之前各num行
  -i 不区分大小写

- head
  -n 前n行，默认前10行

- tail
  -n 后n行，默认后10行

- seq sequence序列
  seq [num] : 从1开始，递增1，一直打印的num
  seq [num_first] [num_end] :从num_first开始，递增1，一直打印到num_end
  seq [num_first] [num_inter] [num_end] :从num_first开始，递增num_inter，一直到num_end

- sed stream editor 流编辑器，实现对文件的增删改替换查。
  参数 -n 取消默认打印，-p 打印
  sed 's#要替换的内容#替换为#g' [file] 只改输出，不改内容，加-i将源文件内容修改

- awk 过滤、输出内容，一门语言。

 