---
layout: post
title: "x86-64 寄存器与栈帧"
date: 2019-10-08
author: "Zzc"
header-style: text
catalog: true
tags:
  - 编程
  - 汇编
  - x86-64
---

### 寄存器

`x86-64`体系有16个通用寄存器：
- `%rax`作为函数返回值使用
- `%rsp`指向栈顶的栈指针寄存器
- `%rdi`,`%rsi`,`%rdx`,`%rcx`,`%r8`,`%r9`用于函数传参，依次对应第1至6个参数
- `%rbx`,`%rbp`,`%r12`,`%r13`,`%r14`,`%r15`用于数据存储，被调用函数在使用之前要保存原值
- `%r10`,`%r11`用于数据存储，调用函数在调用其他函数之前要保存原值

![img](\img\in-post\post-x86-64-register-and-stack\20160508214224799.png)

### 栈帧

