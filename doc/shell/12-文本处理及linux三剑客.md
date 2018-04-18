# 文本处理及linux三剑客

## 1 grep

格式：    
``grep [option] [pattern] [file]``

### 1.1 参数：    
grep一般常用参数

 参数 | 作用 
 ---|---  
 \-i | 忽略大小写的不同，所有大小写视为相同
 \-n | 匹配的内容在其行首显示行号 
 \-v | 反向选择，即显示没有'搜索字符串'内容的那一行 
 \-c | 计算找到'搜索字符串'的次数 
 \-E | 扩展的grep，即egrep 
 \--color=auto | 以特定颜色高亮显示匹配关键字 
 \-w | 只匹配过滤的单词
 \-o | 仅显示出匹配regexp的内容（用于统计出现在文中的次数）
 \-A | After，显示匹配字符串及其后n行的数据 
 \-B | before,显示匹配字符串及其前n行的数据 
 \-C | context，显示匹配字符串及其前后各n行的数据 
 \-a | 在二进制文件中，以文本文件的方式搜索数据 

### 1.2 扩展的正则表达式： grep -E以及egrep

参数 | 作用 
--- | --- 
\+ | 表示重复“一个或一个以上”前面的字符（*是0或多个） 
？ | 表示重复“0个或1个”前面的字符 
\| | 表示同时过滤多个字符串 
() | 分组过滤，后向引用 


### 1.3 元字符

| 正则表达式 | 描述 | 示例 |
| --- | --- | --- |
| \b | 单词边界 | \bcool\b匹配cool，不匹配coolant |
| \B | 非单词边界 | cool\B匹配coolant，不匹配cool |
| \d | 单个数字字符 | b\db匹配b2b，不匹配bcb |
| \D | 单个非数字字符 | b\Db匹配bcb，不匹配b2b |
| \w | 单个单词字符（字母、数字与_） | \w匹配1或a，不匹配& |
| \W | 单个非单词字符 | \W匹配&，不匹配1或a |
| \n | 换行符 | \n匹配一个新行 |
| \s | 单个空白字符 | x\sx匹配x x，不匹配xx |
| \S | 单个非空白字符 | x\Sx匹配xkx，不匹配x x |
| \r | 回车 | \r匹配回车 |
| \t | 横向制表符 | \t匹配一个横向制表符 |
| \v | 垂直制表符 | \v 匹配一个垂直制表符 |
| \f | 换页符 | \f匹配一个换页符 |


# 2 sed

格式：    
``sed [选项] [sed内置命令字符] [输入文件]``    

选项参数：    
 -n 取消默认的sed输出，常与sed内置命令的p连用     
 -i 直接修改文件内容，而不是输出的到终端    
 -r, --regexp-extended,扩展正则表达式，在脚本中使用扩展正则表达式     

内置命令字符：    
 - a append追加文本到指定行后    
 - i insert插入文本到指定行前     
例：     
```
 sed '2a new-text-context' input-file.txt
 sed '2i new-text-context' input-file.txt
```
 - d delete删除指定的行    
例：
 ``sed '2d' input-file.txt``
 - c 用新行取代旧行     
例：   
 ``sed '2c new-context' input-file.txt``
 - s#regexp#replacement# 文本替换    
 常用s###g, g是一个标志，代表全局替换，其中#为定界符，定界符可以是任意符号如:或|等，常用#。
例：    
 ``sed 's#old_context#new_context#g' input-file.txt``
 - p print，表示打印匹配行的内容，通常p会与选项-n一起使用     
例：    
 ``sed -n '2p' input-file.txt``
 - & 代表被替换的内容    
例：将"a"替换为"--a--"     
```
[wxm@basic sed]$ cat abc.txt 
hhha
bbba
ccca
[wxm@basic sed]$ sed '1,2s#a#--&--#g' abc.txt   
hhh--a--
bbb--a--
ccca
```

- 多行操作：    
 n1[,n2]{sed-commands}    
例：
 10{sed-commands} 对第10行进行操作     
 10,20{sed-command} 对10到20行进行操作，包括第10,20行     
 10, +20{sed-command} 对10到30（10+20）行进行操作，包括第10,30行    
 1~2{sed-command} 对1,3,5,7,...行进行操作    
 10, ${sed-command} 对10到最后一行进行操作，包括第10行    
 /context/{sed-command} 对匹配context的行进行操作    
 /context/,/context2/{sed-command} 对匹配context的行到匹配context2的行操作    
 /context/,${sed-command} 对匹配context的行到最后一行进行操作     
 /context/,10 对匹配context的行到第10行进行操作，注意：如果前10行没有匹配到context，sed软件会显示10行以后匹配context的行     
 1,/context/{sed-command} 对第1行到匹配context的行进行操作     
 /context/,+2{sed-command} 对匹配context的行到其后的2行进行操作    

- 分组替换\\\(\\\)和\1     
例：提取"oldboy"
```
[wxm@basic sed]$ echo I am oldboy teacher. | sed 's#^.*am \([a-z].*\) tea.*$#\1#g'
oldboy
[wxm@basic sed]$ 
```

# 3. awk

