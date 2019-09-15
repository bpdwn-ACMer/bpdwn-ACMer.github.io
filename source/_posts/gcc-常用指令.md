---
title: gcc 常用指令
date: 2019-09-15 21:10:09
categories: Linux程序设计
tags:
---

## gcc工作流程
- 预处理 -E
	- 宏替换
	- 头文件展开
	- 去掉注释
	- `gcc -E hello.c -o hello.i`
- 编译 -S
	- `gcc -S hello.i -o hello.s`
	- 汇编文件
- 汇编 -C
	- `gcc -c hello.s -o hello.o`
	- 二进制文件
- 链接
	- `gcc hello.o -o hello`

## gcc常用参数
```shell
[bpdwn@bpdwn-pc gcc_study]$ tree
.
├── include
│   └── head.h
└── sum.c

1 directory, 2 files
[bpdwn@bpdwn-pc gcc_study]$ cat sum.c 
#include <stdio.h>
#include "head.h"

int main() {
	int a = 2;
	int b = 3;
	int ans = a + b;
	printf("%d\n", ans);
#ifdef DEBUG
	printf("ans = %d + %d\n", 2, 3);
#endif
	return 0;
}
[bpdwn@bpdwn-pc gcc_study]$ 

```
- -v 版本信息
- -I 指定头文件路径
	`gcc sum.c -I ./include/`
- -o 制定输出文件名
- -D 编译时指定宏
	```shell
	[bpdwn@bpdwn-pc gcc_study]$ gcc sum.c -I ./include
	[bpdwn@bpdwn-pc gcc_study]$ ./a.out 
	5
	[bpdwn@bpdwn-pc gcc_study]$ gcc sum.c -I ./include -D DEBUG
	[bpdwn@bpdwn-pc gcc_study]$ ./a.out 
	5
	ans = 2 + 3
	[bpdwn@bpdwn-pc gcc_study]$ 
	```
- -Wall 警告提示
- -On 优化代码，n是优化级别:1,2,3

