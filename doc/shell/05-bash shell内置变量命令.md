# bash shell内置变量命令

常用的有echo、eval、exec、export、readonly、read、shift、wait、exit和点(.)。
最常用的：echo、export、read、exit

## 1 echo

命令格式：echo args #<==可以使字符串和变量的组合

## 2 eval

命令格式:eval args   

功能：当shell程序执行到eval语句时，shell读入参数args，并把它们组合成一个新的命令，然后执行。   
也就是将字符串形式的命令进行解析执行。

## 3 exec  

命令格式：exec命令参数 

功能：exec命令能够在不创建新的子进程的前提下，转去执行指定的命令，当指定的命令执行完毕后，此进程终止。

## 4 read

shell变量处理可以**直接赋值**或**脚本传参**外，还可以使用**read**命令从标准输入获得。

### 4.1 语法格式

`` read [参数] [变量名]``

常用参数：
  - -p ：prompt，设置提示信息
  - -t : timeout，设置输入等待的时间，单位默认为秒

```bash
oldboy@server:~$ read -p "please input two number:" a1 a2
please input two number:1 2
oldboy@server:~$ echo $a1 $a2
1 2
```

## 5 shift  

命令格式： shift-Shift positional parameters

功能：shift语句会按如下方式重新命名所有的位置参数变量，即$2成为$1，$3成为$2，以此类推，在程序中每使用一次shift语句，都会使所有的位置参数依次向左移动一个位置，并使位置参数$#减1，直到减到0为止。

