# 子shell及shell嵌套

## 1 子shell

### 1.1 带"\&"提交后台作业

```bash
#!/bin/sh
...
{
    command
    ...
}&
```

特点：
  - 在shell中使用"\&"可以产生子Shell
  - 由"\&"产生的子shell可以直接引用父shell定义的普通变量
  - 由"\&"产生的子shell中定义的变量不能被父shell引用
  - 在shell中使用"\&"可以实现其他程序的多线程并发功能

### 1.2 使用“管道”功能

```bash
#!/bin/sh
...
echo "" | {
    command
    ...
}
```

特点：
  - 在shell中使用管道可以产生子shell
  - 由管道产生的子shell可以直接引用父shell定义的普通变量
  - 由管道产生的子shell中定义的变量不能被父shell引用
  - 由管道产生的子shell不能异步执行，只能在执行完毕后才能返回到父shell环境

### 1.3 使用"()"功能

```bash
#!/bin/sh
...
(
    command
    ...
)
```

特点：
  - 在shell中使用\(\)可以产生子shell
  - 由\(\)产生的子shell可以直接引用父shell定义的普通变量
  - 由\(\)产生的子shell中定义的变量不能被父shell引用
  - 由\(\)产生的子shell不能异步执行，只能在执行完毕后才能返回到父shell环境

### 1.4 通过调用外部shell脚本产生子shell

```bash
#!/bin/sh
...
sh script.sh
...
```

特点：
  - 在shell中使用sh命令可以产生子shell
  - 由sh命令产生的子shell不能引用父shell定义的普通变量，只能引用父shell定义的环境变量
  - 由sh命令产生的子shell中定义的变量不能被父shell引用
  - 由sh命令产生的子shell不能异步执行，只能在执行完毕后才能返回到父shell环境

## 2 shell调用脚本的模式说明

### 2.1 fork

调用方式：   
``/bin/sh script.sh``

结果：   
产生子shell，子shell执行过程中父shell阻塞；当子shell执行完毕后，回到父shell环境，父shell继续执行后续脚本。

### 2.2 exec

调用方式：  
``exec script.sh``

结果：   
替代当前shell，执行完毕后，当前shell退出。

### 2.3 source

调用方式：  
``source script.sh``   
或  
``. source.sh``

结果：   
在当前shell执行，不会产生子shell，执行完毕后当前shell会继续执行后续的脚本。





