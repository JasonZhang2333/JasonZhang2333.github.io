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
 `gdb [*program* [_core_]]` | 使用coredump调试程序
 `b` [*file*:]*function* | 在文件的函数上设断点
 `r [arglist]` | 设置参数启动程序
 `bt` | 打印程序栈
 `p expr` | 打印表达式的值
 `c` | 继续运行程序
 `n` | 越过程序调用运行到下一行
 `s` | 进入程序调用运行到下一行
 `q` | 退出GDB，`Ctrl-C`/`Ctrl-D`也可
 `help [command]` | 命令帮助
 

 ### 程序运行命令
 
 命令 | 说明
 -|-
 `r [arglist]` | 设置参数启动程序
 `r` | 使用当前参数启动程序
 `r ... <inf >outf` | 重定向输入输出运行程序
 `kill` | 终止当前程序
 `tty dev` | 使用dev作为下次运行的标准输入输出
 `set args arglist`
