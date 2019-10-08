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

`x86-64`体系有16个通用寄存器：
- `%rax`作为函数返回值使用
- `%rsp`指向栈顶的栈指针寄存器
- 