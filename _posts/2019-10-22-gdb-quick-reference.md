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
 `gdb *program* [*core*]` | [使用coredump]调试程序
 `b [*file*:]*function*` | 在[文件的]函数上设断点
 `r [*arglist*]` | [设置参数]启动程序
 `bt` | 打印程序栈
 `p *expr*` | 打印表达式的值
 `c` | 继续运行程序
 `n` | 越过程序调用运行到下一行
 `s` | 进入程序调用运行到下一行
 

