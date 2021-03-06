# linux通配符和正则表达式
通配符一般用于命令行bash环境，而linux正则表达式用于grep，sed，awk场景。

## 1. 通配符
| 通配符 | 作用 | 重要程度 |
| ---   | --- | --- |
| * |  代表任意(0到多个)字符 | 5 |
| ? | 代表任意1个字符 | 5 |
| ; | 连续不同命令的分隔符 | 5 |
| # | 配置文件注释 | 5 |
| 单竖线 | 管道 | 5 |
| ~ | 用户的家目录 | 5 |
| - | 上一次的目录 | 5 |
| $ | 变量前需要加的符号 | 3 |
| / | 路径分隔符号 | 3 |
| >或1> | 重定向，覆盖 | 5 |
| >> | 追加重定向，追加 | 5 |
| < | 输入重定向 | 5 |
| << | 追加输入重定向 | 3 |
| ' | 单引号，不具有变量置换功能，输出时所见即所得 | 5 |
| " | 双引号，具有变量置换功能，解析后输出，不加引号相当于双引号 | 5 |
| \` | tab键上面的键，反引号，两个\`\`中间为命令，会先执行，等价$() | 5 |
| {} | 中间为命令区块组合或内容序列 | 3 |
| ！ | 逻辑运算符中的"非"(not) | 3 |
| && | 当前一个指令执行成功时，执行后一个命令 | 3 |
| 双竖线 | 当前一个指令执行失败时，执行后一个命令 | 3 |
| .. | 上一级目录 | 4 |
| . | 当前目录 | 4 |

## 2. 正则表达式

### 2.1 环境准备

- 调整别名
  alias grep='grep --color=auto'
- 注意字符集：可能带来的问题
  export LC_ALL=C


### 2.2 基础表达式

| 字符 | 释义 |
| --- | --- |
| ^word | 匹配以word开头的内容。vi/vim里^代表一行的开头 |
| word$ | 匹配以word结尾的内容。vi/vim里$代表一行的末尾 |
| ^$ | 表示空行 |
| . | 代表且只能代表任意一个字符 |
| \ | 转义符号，转义特殊字符 |
| * | 重复0个或多个前面的一个字符，例o*匹配没有o，有1个o或多个ooo |
| .* | 匹配所有字符 |
| [abc] | 匹配字符集合内的任意一个字符[a-zA-Z],[0-9] |
| [^abc] | 匹配不包含^后的任意一个字符的内容 |
| a\{n,m\} | 重复n到m次，前一个重复的字符。如果用egrep/sed -r可以去掉斜线 |
| a\{n,\} | 重复至少n次，前一个重复的字符。如果用egrep/sed -r可以去掉斜线 |
| a\{n\} | 重复n次 |
| a\{,m\} | 至多m次 |
注意：egrep或sed -r过滤一般特殊字符可以不转义。

