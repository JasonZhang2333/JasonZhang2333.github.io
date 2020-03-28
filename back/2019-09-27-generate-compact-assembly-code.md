---
layout:     post
title:      "生成简洁汇编代码"
date:       2019-09-27
author:     "Zzc"
header-style: text
tags:
    - 编程
    - 汇编
---

```shell
$ gcc -fno-stack-protector -fomit-frame-pointer -fno-asynchronous-unwind-tables -S demo.c
```

- `-fno-stack-protector`：去掉 stack 保护，stack protector 用于检查 stack 是否被踩
- `-fomit-frame-pointer`：不用 fp 寄存器 rbp/ebp，直接用 stack 寄存器 rsp/esp 就好了
- `-fno-asynchronous-unwind-tables`：消除 .eh_frame section
