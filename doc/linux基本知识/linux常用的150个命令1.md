# linux常用的150个命令1

## 查看命令帮助的几种方法
- [命令] --help 适用于一般命令，非内置命令
- man [命令] 适用于一般命令，非内置命令
- help [命令] 适用于内置命令
- info

## 1. pwd [-LP]
-L --logical  打印的是$PWD的值，即当前目录的值
-P --physical 如果目录是一个软连接或者符号链接的话，显示其真实链接所在路径
例：
```
wxm@server:~/test/linux-cmd/link$ ls -l
总用量 0
lrwxrwxrwx 1 wxm wxm 19 3月  28 21:35 arm -> /opt/crosstools/arm
wxm@server:~/test/linux-cmd/link$ cd arm && pwd -P
/opt/crosstools/arm
wxm@server:~/test/linux-cmd/link/arm$ pwd -L
/home/wxm/test/linux-cmd/link/arm
wxm@server:~/test/linux-cmd/link/arm$ 
```
man pwd
```
NAME
       pwd - print name of current/working directory

SYNOPSIS
       pwd [OPTION]...

DESCRIPTION
       Print the full filename of the current working directory.

       -L, --logical
              use PWD from environment, even if it contains symlinks

       -P, --physical
              avoid all symlinks

       --help display this help and exit

       --version
              output version information and exit
```
## 2. mkdir
-p 常用，递归创建，并忽略产生的错误
-v 显示创建的过程
```
用法：mkdir [选项]... 目录...
Create the DIRECTORY(ies), if they do not already exist.

必选参数对长短选项同时适用。
  -m, --mode=MODE   set file mode (as in chmod), not a=rwx - umask
  -p, --parents     no error if existing, make parent directories as needed
  -v, --verbose     print a message for each created directory
  -Z                   set SELinux security context of each created directory
                         to the default type
      --context[=CTX]  like -Z, or if CTX is specified then set the SELinux
                         or SMACK security context to CTX
      --help            显示此帮助信息并退出
      --version         显示版本信息并退出
```

## 3.  cd
-L 及-P与pwd类型
```
help cd
cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.
-L      force symbolic links to be followed: resolve symbolic links in
        DIR after processing instances of `..'
-P      use the physical directory structure without following symbolic
        links: resolve symbolic links in DIR before processing instances
        of `..'
```
例：
```
wxm@server:~/test/linux-cmd/link$ ll
总用量 8
drwxrwxr-x 2 wxm wxm 4096 3月  28 21:35 ./
drwxrwxr-x 4 wxm wxm 4096 3月  28 21:34 ../
lrwxrwxrwx 1 wxm wxm   19 3月  28 21:35 arm -> /opt/crosstools/arm/
wxm@server:~/test/linux-cmd/link$ cd -P arm
wxm@server:/opt/crosstools/arm$ 
```
常用用法：
- cd - : 返回上一次目录，实质是从$OLDPWD变量获取路径，并跳转
- cd ~ : 返回家目录


## 4. tree

```
tree --help
usage: tree [-acdfghilnpqrstuvxACDFJQNSUX] [-H baseHREF] [-T title ]
        [-L level [-R]] [-P pattern] [-I pattern] [-o filename] [--version]
        [--help] [--inodes] [--device] [--noreport] [--nolinks] [--dirsfirst]
        [--charset charset] [--filelimit[=]#] [--si] [--timefmt[=]<f>]
        [--sort[=]<name>] [--matchdirs] [--ignore-case] [--] [<directory list>]
  ------- Listing options -------
  -a            All files are listed.
  -d            List directories only.
  -l            Follow symbolic links like directories.
  -f            Print the full path prefix for each file.
  -x            Stay on current filesystem only.
  -L level      Descend only level directories deep.
  -R            Rerun tree when max dir level reached.
  -P pattern    List only those files that match the pattern given.
  -I pattern    Do not list files that match the given pattern.
  --ignore-case Ignore case when pattern matching.
  --matchdirs   Include directory names in -P pattern matching.
  --noreport    Turn off file/directory count at end of tree listing.
  --charset X   Use charset X for terminal/HTML and indentation line output.
  --filelimit # Do not descend dirs with more than # files in them.
  --timefmt <f> Print and format time according to the format <f>.
  -o filename   Output to file instead of stdout.
  -------- File options ---------
  -q            Print non-printable characters as '?'.
  -N            Print non-printable characters as is.
  -Q            Quote filenames with double quotes.
  -p            Print the protections for each file.
  -u            Displays file owner or UID number.
  -g            Displays file group owner or GID number.
  -s            Print the size in bytes of each file.
  -h            Print the size in a more human readable way.
  --si          Like -h, but use in SI units (powers of 1000).
  -D            Print the date of last modification or (-c) status change.
  -F            Appends '/', '=', '*', '@', '|' or '>' as per ls -F.
  --inodes      Print inode number of each file.
  --device      Print device ID number to which each file belongs.
  ------- Sorting options -------
  -v            Sort files alphanumerically by version.
  -t            Sort files by last modification time.
  -c            Sort files by last status change time.
  -U            Leave files unsorted.
  -r            Reverse the order of the sort.
  --dirsfirst   List directories before files (-U disables).
  --sort X      Select sort: name,version,size,mtime,ctime.
  ------- Graphics options ------
  -i            Don't print indentation lines.
  -A            Print ANSI lines graphic indentation lines.
  -S            Print with CP437 (console) graphics indentation lines.
  -n            Turn colorization off always (-C overrides).
  -C            Turn colorization on always.
  ------- XML/HTML/JSON options -------
  -X            Prints out an XML representation of the tree.
  -J            Prints out an JSON representation of the tree.
  -H baseHREF   Prints out HTML format with baseHREF as top directory.
  -T string     Replace the default HTML title and H1 header with string.
  --nolinks     Turn off hyperlinks in HTML output.
  ---- Miscellaneous options ----
  --version     Print version and exit.
  --help        Print usage and this help message and exit.
  --            Options processing terminator.
```

## 5. touch
- 创建新的空文件
- 修改时间戳
常用参数：
-a 只更改访问时间 atime
-m 只更改修改时间 mtime

```
touch --help
用法：touch [选项]... 文件...
Update the access and modification times of each FILE to the current time.

A FILE argument that does not exist is created empty, unless -c or -h
is supplied.

A FILE argument string of - is handled specially and causes touch to
change the times of the file associated with standard output.

必选参数对长短选项同时适用。
  -a                    只更改访问时间
  -c, --no-create       不创建任何文件
  -d, --date=字符串     使用指定字符串表示时间而非当前时间
  -f                    (忽略)
  -h, --no-dereference          会影响符号链接本身，而非符号链接所指示的目的地
                                (当系统支持更改符号链接的所有者时，此选项才有用)
  -m                    只更改修改时间
  -r, --reference=FILE   use this file's times instead of current time
  -t STAMP               use [[CC]YY]MMDDhhmm[.ss] instead of current time
      --time=WORD        change the specified time:
                           WORD is access, atime, or use: equivalent to -a
                           WORD is modify or mtime: equivalent to -m
      --help            显示此帮助信息并退出
      --version         显示版本信息并退出

请注意，-d 和-t 选项可接受不同的时间/日期格式。
```

## 6. useradd 
添加用户
```
useradd --help
用法：useradd [选项] 登录
      useradd -D
      useradd -D [选项]

选项：
  -b, --base-dir BASE_DIR       新账户的主目录的基目录
  -c, --comment COMMENT         新账户的 GECOS 字段
  -d, --home-dir HOME_DIR       新账户的主目录
  -D, --defaults                显示或更改默认的 useradd 配置
  -e, --expiredate EXPIRE_DATE  新账户的过期日期
  -f, --inactive INACTIVE       新账户的密码不活动期
  -g, --gid GROUP               新账户主组的名称或 ID
  -G, --groups GROUPS   新账户的附加组列表
  -h, --help                    显示此帮助信息并推出
  -k, --skel SKEL_DIR   使用此目录作为骨架目录
  -K, --key KEY=VALUE           不使用 /etc/login.defs 中的默认值
  -l, --no-log-init     不要将此用户添加到最近登录和登录失败数据库
  -m, --create-home     创建用户的主目录
  -M, --no-create-home          不创建用户的主目录
  -N, --no-user-group   不创建同名的组
  -o, --non-unique              允许使用重复的 UID 创建用户
  -p, --password PASSWORD               加密后的新账户密码
  -r, --system                  创建一个系统账户
  -R, --root CHROOT_DIR         chroot 到的目录
  -s, --shell SHELL             新账户的登录 shell
  -u, --uid UID                 新账户的用户 ID
  -U, --user-group              创建与用户同名的组
  -Z, --selinux-user SEUSER             为 SELinux 用户映射使用指定 SEUSER
      --extrausers              Use the extra users database
```
## 7. passwd
为用户设置或修改密码
```
passwd --help
用法：passwd [选项] [登录]

选项：
  -a, --all                     报告所有帐户的密码状态
  -d, --delete                  删除指定帐户的密码
  -e, --expire                  强制使指定帐户的密码过期
  -h, --help                    显示此帮助信息并推出
  -k, --keep-tokens             仅在过期后修改密码
  -i, --inactive INACTIVE       密码过期后设置密码不活动为 INACTIVE
  -l, --lock                    锁定指定的帐户
  -n, --mindays MIN_DAYS        设置到下次修改密码所须等待的最短天数
                                为 MIN_DAYS
  -q, --quiet                   安静模式
  -r, --repository REPOSITORY   在 REPOSITORY 库中改变密码
  -R, --root CHROOT_DIR         chroot 到的目录
  -S, --status                  报告指定帐户密码的状态
  -u, --unlock                  解锁被指定帐户
  -w, --warndays WARN_DAYS      设置过期警告天数为 WARN_DAYS
  -x, --maxdays MAX_DAYS        设置到下次修改密码所须等待的最多天数
                                为 MAX_DAYS
```

## 8. uname
打印系统信息
```
用法：uname [选项]...
输出一组系统信息。如果不跟随选项，则视为只附加-s 选项。

  -a, --all                     以如下次序输出所有信息。其中若-p 和
                                -i 的探测结果不可知则被省略：
  -s, --kernel-name             输出内核名称
  -n, --nodename                输出网络节点上的主机名
  -r, --kernel-release          输出内核发行号
  -v, --kernel-version     print the kernel version
  -m, --machine            print the machine hardware name
  -p, --processor          print the processor type (non-portable)
  -i, --hardware-platform  print the hardware platform (non-portable)
  -o, --operating-system   print the operating system
```

## 9. hostname
显示和设置主机名，默认是显示主机名，设置主机名可以hostname 名字（临时生效）
```
host --help
host: illegal option -- -
Usage: host [-aCdlriTwv] [-c class] [-N ndots] [-t type] [-W time]
            [-R number] [-m flag] hostname [server]
       -a is equivalent to -v -t ANY
       -c specifies query class for non-IN data
       -C compares SOA records on authoritative nameservers
       -d is equivalent to -v
       -l lists all hosts in a domain, using AXFR
       -i IP6.INT reverse lookups
       -N changes the number of dots allowed before root lookup is done
       -r disables recursive processing
       -R specifies number of retries for UDP packets
       -s a SERVFAIL response should stop query
       -t specifies the query type
       -T enables TCP/IP mode
       -v enables verbose output
       -w specifies to wait forever for a reply
       -W specifies how long to wait for a reply
       -4 use IPv4 query transport only
       -6 use IPv6 query transport only
       -m set memory debugging flag (trace|record|usage)
       -V print version number and exit
```

## 10. 关机
  shutdown -h now
  halt
  init 0
```
wxm@server:~$ shutdown --help
shutdown [OPTIONS...] [TIME] [WALL...]

Shut down the system.

     --help      Show this help
  -H --halt      Halt the machine
  -P --poweroff  Power-off the machine
  -r --reboot    Reboot the machine
  -h             Equivalent to --poweroff, overridden by --halt
  -k             Don't halt/power-off/reboot, just send warnings
     --no-wall   Don't send wall message before halt/power-off/reboot
  -c             Cancel a pending shutdown
```
## 11. 重启
  reboot
  init 6
  shutdown -r now

## 12. history

```
wxm@server:~$ help history
history: history [-c] [-d 偏移量] [n] 或 history -anrw [文件名] 或 history -ps 参数 [参数...]
    显示或操纵历史列表。
    
    带行号显示历史列表，将每个被修改的条目加上前缀 `*'。
    参数 N 会仅列出最后的 N 个条目。
    
    选项：
      -c        删除所有条目从而清空历史列表。
      -d        偏移量 从指定位置删除历史列表。
      -a        将当前会话的历史行追加到历史文件中
      -n        从历史文件中读取所有未被读取的行
      -r        读取历史文件并将内容追加到历史列表中
      -w        将当前历史写入到历史文件中,并追加到历史列表中
      -p        对每一个 ARG 参数展开历史并显示结果,而不存储到历史列表中
      -s        以单条记录追加 ARG 到历史列表中
    
    如果给定了 FILENAME 文件名，则它将被作为历史文件。否则
    如果 $HISTFILE 变量有值的话使用之，不然使用 ~/.bash_history 文件。
    
    如果 $HISTTIMEFORMAT 变量被设定并且不为空，它的值会被用于
    strftime(3) 的格式字符串来打印与每一个显示的历史条目想关联的时
    间戳，否则不打印时间戳。
```
设置历史记录最大数量：
``export HISTSIZE=5``
设置后使用history只记录最后5条。

设置历史记录文件.bash_history最大保存条目：
```
export HISTFILESIZE=3
cat ~/.bash_history
```

## 13. cp
  -a 复制文件保持文件所有属性
  -i 覆盖复制时提示警告信息
  cp /etc/ssh/sshd_config{,.ori} 相当于
  cp /etc/ssh/sshd_config /etc/ssh/sshd_config.ori 

## 14. ls
  -a , --all  包含隐藏文件的所有文件
  -l , 详细信息
  -h , 文件大小以人类可识别方式显示，如4096->4K
  -d , 只显示目录
  -F , 为目录添加/
  -r , 倒序排序
  -t , 按照修改时间排序，默认升序排序

## 15. mv
  -i , 覆盖文件或找不到文件时提示警告信息
  
## 16. rm
正确姿势：
1. 使用mv命令移到到/tmp替代删除动作
2. cd 目的目录 ; find . -type f(d) -name ""|xargs rm -i

## 17. find

