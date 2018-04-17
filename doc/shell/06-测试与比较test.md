# 条件测试与比较

测试结构：  
  - ``test expression``  
  - ``[ expression ]``  

## 1 文件测试
格式：  
  - ``test file_operator FILE``
  - ``[ file_operator FILE ]``  

文件测试符：  

文件测试  |  说明  
---|---
-f FILE | 英文file，文件存在且为普通文件则真，即测试表达式成立
-d FILE | 英文directory，文件存在且为目录则真，即测试表达式成立
-s FILE | 英文size，文件存在且文件大小不为0则真，即测试表达式成立
-e FILE | 英文exist，文件存在则真，即测试表达式成立
-r FILE | 英文read，文件存在且可读则真
-w FILE | write，文件存在且可读则真
-x FILE | excutable，文件存在且可执行则真
-b FILE | block，文件存在且是个块文件时则真
-c FILE | char，文件存在且是个字符设备时则真
-L FILE | Link，文件存在且为链接文件则真
-p FILE | pipe，文件存在且是个管道文件时则真
-S FILE | socket，文件存在且为socket文件时则真
-g FILE | sgid，文件存在且设置了SGID时则真
-u FILE | suid，文件存在且设置了SUID时则真
-k FILE | sticky，文件存在且设置了sticky属性时则真
-G FILE | 文件存在且属于有效的用户组时则真
-O FILE | 文件存在且属于有效的用户时则真
f1 -nt f2 | newer than，文件f1比文件f2新则真
f1 -ot f2 | older than，文件f1比文件f2旧则真

例：文件存在则echo 1，否则echo 0   
``[ -f oldboy ]&&echo 1 || echo 0``  
最好加上双引号，否则结果肯能不太准：  
``[ -f "oldboy" ]&&echo 1 || echo 0``  

## 2 字符串测试
格式：  
  - ``test string_operator STRING``  
  - ``[ string_operator STRING ]``    

字符串测试符：  

字符串测试 | 说明  
---|---
-z "string" | 字符串string为空时返回真
-n "string" | 字符串string非空时返回真
"string1" = "string2" | 字符串string1和string2相同时返回真
"string1" != "string2" | 字符串string1和string2不相同时返回真
"string1" \> "string2" | 按照字典排序，字符串string1排在string2之**前**时返回真
"string1" \< "string2" | 按照字典排序，字符串string1排在string2之**后**时返回真

## 3 整数比较
格式：
  - ``test "num1" num_operator "num2"``  
  - ``[ "num1" num_operator "num2" ]`` 

整数测试符：  

整数比较 | 说明  
---|---
"num1" -eq "num2" | 如果num1等于num2则返回真  
"num1" -ne "num2" | 如果num1不等于num2则返回真  
"num1" -gt "num2" | 如果num1大于num2则返回真  
"num1" -lt "num2" | 如果num1小于num2则返回真  
"num1" -ge "num2" | 如果num1大于等于num2则返回真  
"num1" -le "num2" | 如果num1小于等于num2则返回真  

另外，还可以在(())和[[]]中使用比较运算符进行比较：  

整数比较 | 说明  
---|---
"num1" == "num2" | 如果num1等于num2则返回真，"=="与"="效果一样  
"num1" != "num2" | 如果num1不等于num2则返回真  
"num1" > "num2" | 如果num1大于num2则返回真  
"num1" < "num2" | 如果num1小于num2则返回真  
"num1" >= "num2" | 如果num1大于等于num2则返回真  
"num1" <= "num2" | 如果num1小于等于num2则返回真 


## 4 逻辑测试符和逻辑运算符

### 4.1 逻辑测试符   
逻辑测试用于一个表达式中连接多个测试条件，并返回整个表达式的值  
格式： 
  - ``test expression1 operator expression2``  
  - ``[ expression1 operator expression2 ]``  

逻辑运算 | 说明
---|---
\! expression | 非，如果expression为真，则返回假
expression1 -a expression2 | 与and，expression1和expression2同时为真，返回真
expression1 -o expression2 | 或or，expression1和expression2只有有一个为真，返回真

### 4.2 逻辑运算符  
逻辑运算用于多个表达式之间进行测试
格式：  
  - ``[ expression1 ] operator [ expression ]``   
  - ``[[ expression1 operator expression2 ]]``

逻辑运算符 | 说明
---|---
\! | 逻辑非，对真假取反
&& | 逻辑与，连接两个表达式，只有两个表达式均为真时结果才真
\|\| | 逻辑或，连接两个表达式，只要有一个表达式为真结果就为真  

例：  
``[ -f $f1 ] && [ -f $f2 ] && echo 1 || echo 0``  
等价于``[ -f $f1 -a -f $f2 ] && echo 1 || echo 0``  
或者``[[ -f $f1 && -f $f2 ]] && echo 1 || echo 0``



