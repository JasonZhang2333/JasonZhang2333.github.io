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
`b` ... if *expr* | 设置条件断点
`cond` *n* [*expr*] | 设置断点*n*为条件断点
`tbreak` ... | 设置临时断点，触发后消失
`rbreak` *regex* | 在正则匹配的所有函数设置断点
`watch` *expr* | 观察表达式值
`catch` *event* | 设置`catch`，`throw`，`exec`，`fork`等事件触发断点
`info break` | 显示所有断点
`info watch` | 显示所有观察点
`clear` | 清除下条指令的断点
`clear` [*file*:]*func* | 清除函数断点
`clear` [*file*:]*line* | 清除某行断点
`delete` [*n*] | 清除断点
`disable` [*n*] | 使某断点失效
`enable` [*n*] | 启用断点
`enable del` [*n*] | 启用断电，触发后删除
`ignore` *n* *count* | 忽略断点几次
`commands` *n* [silent] *command-list* end | 到达断点时执行命令
