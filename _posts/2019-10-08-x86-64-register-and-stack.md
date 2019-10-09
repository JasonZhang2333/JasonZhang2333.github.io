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

![img](\img\in-post\post-x86-64-register-and-stack\20160508214803291.png)

机器语言中，GCC把过程转化成栈帧(frame)，由`%rbp`指向栈帧开始，`%rsp`指向栈顶

```cpp
#include <cstdio>

int foo(int x) {
    int array[]={1,3,5};
    return array[x];
}

int main() {
    int i = 1;
    int j = foo(i);
    printf("i=%d,j=%d\n", i, j); 
    return 0;
}
```

命令行调用生成汇编语言

```shell
gcc -S test.cpp
```

```assembly
    .file   "test.cpp"
    .text
    .globl  _Z3fooi
    .type   _Z3fooi, @function
_Z3fooi:
.LFB0:
    .cfi_startproc
    movl    %edi, -20(%rsp)
    movl    $1, -16(%rsp)
    movl    $3, -12(%rsp)
    movl    $5, -8(%rsp)
    movl    -20(%rsp), %eax
    cltq
    movl    -16(%rsp,%rax,4), %eax
    ret
    .cfi_endproc
.LFE0:
    .size   _Z3fooi, .-_Z3fooi
    .section    .rodata
.LC0:
    .string "i=%d,j=%d\n"
    .text
    .globl  main
    .type   main, @function
main:
.LFB1:
    .cfi_startproc
    subq    $24, %rsp
    .cfi_def_cfa_offset 32
    movl    $1, 12(%rsp)
    movl    12(%rsp), %eax
    movl    %eax, %edi
    call    _Z3fooi
    movl    %eax, 8(%rsp)
    movl    8(%rsp), %edx
    movl    12(%rsp), %eax
    movl    %eax, %esi
    movl    $.LC0, %edi
    movl    $0, %eax
    call    printf
    movl    $0, %eax
    addq    $24, %rsp
    .cfi_def_cfa_offset 8
    ret
    .cfi_endproc
.LFE1:
    .size   main, .-main
    .ident  "GCC: (GNU) 4.8.5 20150623 (Red Hat 4.8.5-4)"
    .section    .note.GNU-stack,"",@progbits
```

