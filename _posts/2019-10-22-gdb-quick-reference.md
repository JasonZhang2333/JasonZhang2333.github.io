---
layout: post
title: "GDB手册"
date: 2019-10-11
author: "Zzc"
header-style: text
catalog: true
tags:
  - 编程
  - C++
  - GDB
  - 调试
---

### 基本命令

命令 | 说明
-|-
 `gdb [program [core]]` | 使用coredump调试程序
 `b [file:]function` | 在文件的函数上设断点
 `r [arglist]` | 设置参数启动程序
 `bt` | 打印程序栈
 `p expr` | 打印表达式的值
 `c` | 继续运行程序
 `n` | 越过程序调用运行到下一行
 `s` | 进入程序调用运行到下一行
 `q` | 退出GDB，`Ctrl-C`/`Ctrl-D`也可
 `help [command]` | 命令帮助
 
 ### 运行命令
 
 
