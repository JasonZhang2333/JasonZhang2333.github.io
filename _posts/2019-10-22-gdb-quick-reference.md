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
`gdb` [*program* [*core*]] | 使用coredump调试程序
`b` [*file*:]*function* | 在文件函数上设断点
`r` [*arglist*] | 设置参数启动程序
`bt` | 打印程序栈
`p` *expr* | 打印表达式的值
`c` | 继续运行程序
`n` | 越过程序调用运行到下一行
`s` | 进入程序调用运行到下一行
`q` | 退出GDB，`Ctrl-C`/`Ctrl-D`也可
`help` [*command*] | 命令帮助

### 运行程序

命令 | 说明
-|-
`r` [*arglist*] | 设置参数启动程序
`r` | 使用当前参数启动程序
`r` ... \<*inf* \>*outf* | 重定向输入输出运行程序
`kill` | 终止当前程序
`tty` *dev* | 使用dev作为下次运行的标准输入输出
`set args` *arglist* | 设置下次启动程序的参数
`show args` | 显示参数列表
`show env` | 显示所有环境变量
`show env` *var* | 显示环境变量值
`set env` *var* *string* | 设置环境变量值
`unset env` *var* | 移除环境变量

### shell命令

命令 | 说明
-|-
`cd` *dir* | 更改工作目录
`pwd` | 显示工作目录
`make` ... | 调用`make`命令
`shell` *cmd* | 调用任何`shell`命令

### 断点命令

命令 | 说明
-|-
`b` [*file*:]*function* | 在文件函数上设断点
`b` [*file*:]*line* | 在文件某行上设断点
`b` +/-*offset* | 在当前位置前后偏移设置断点
`b` \**addr* | 在某地址设置断点
`b` | 在下条指令设置断点
