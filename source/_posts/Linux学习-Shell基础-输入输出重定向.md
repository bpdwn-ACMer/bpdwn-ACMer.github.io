---
title: Linux学习-Shell基础-输入输出重定向
date: 2019-01-26 22:53:52
categories: Linux
tags: [Linux,Shell,学习]
---

#### 标准输入输出

设备  |设备文件名 |文件描述符|类型
------|---------- |----------|------------
键盘  |/dev/stdin |   0      |标准输入
显示器|/dev/stdout|   1      |标准输出
显示器|/dev/stderr|   2      |标准错误输出


----------
 
#### 输出重定向

类型          |   符号  |作用
--------------|---------|-----------------------------------------------------
标准输出重定向|命令>文件|以覆盖方式把命令的正确输出输出到指定的文件中
标准输出重定向|命令>>文件|以追加方式把命令的正确输出输出到指定的文件中
标准错误输出重定向|错误命令>文件|以覆盖方式把命令的错误输出输出到指定的文件中
标准错误输出重定向|错误命令>>文件|以追加方式把命令的错误输出输出到指定的文件中


```
[bpdwn@localhost ~]$ touch right.out
[bpdwn@localhost ~]$ touch error.out
[bpdwn@localhost ~]$ ls > right.out 
[bpdwn@localhost ~]$ cat right.out 
data.txt
error.out
hello.sh
right.out
test.cpp
[bpdwn@localhost ~]$ lst 2> error.out 
[bpdwn@localhost ~]$ cat error.out 
-bash: lst: 未找到命令
```

- 2与>(>>)之间不能有空格

----------

#### 正确输出和错误输出同时保存

命令|作用
--------------------|--------------------------------------------------
命令 > 文件 2>&1    |以覆盖方式把正确输出和错误输出都保存到同一文件当中
命令 >> 文件 2>&1   |以追加方式把正确输出和错误输出都保存到同一文件当中
命令 &> 文件 2>&1   |以覆盖方式把正确输出和错误输出都保存到同一文件当中
命令 &>> 文件 2>&1  |以追加方式把正确输出和错误输出都保存到同一文件当中
命令>>文件1 2>>文件2|把正确的输出追加到文件1中，把错误的输出到文件2中


----------
#### 输出重定向

命令|作用
----|----
命令<文件|把文件作为命令的输入 
命令<<文件|把两个符号直接的内容作为命令的输入 

```
wc [选项][文件名]
-c 统计字节数
-w 统计单词数
-l 统计行数
```

```
[bpdwn@localhost ~]$ cat test.txt 
hello
 world
apple

[bpdwn@localhost ~]$ wc < test.txt 
 4  3 20
[bpdwn@localhost ~]$ 
/*输出行 单词数 字符数*/

[bpdwn@localhost ~]$ wc << hello
> world
> apple
> orange
> hello
 3  3 19
[bpdwn@localhost ~]$ 
/*统计两个hello之间的字符*/
```

