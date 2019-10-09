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

###### 基本结构

![img](\img\in-post\post-x86-64-register-and-stack\20160508214803291.png)

机器语言中，GCC把过程转化成栈帧(frame)，由`%rbp`指向栈帧开始，`%rsp`指向栈顶

```cpp
//test.cpp
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

```shell
gcc -S test.cpp
```

命令行调用生成汇编语言

```
# test.s
    .file   "test.cpp"
    .text
    .globl  _Z3fooi
    .type   _Z3fooi, @function
_Z3fooi:
.LFB0:
    pushq   %rbp
    movq    %rsp, %rbp
    movl    %edi, -20(%rbp)
    movl    $1, -16(%rbp)
    movl    $3, -12(%rbp)
    movl    $5, -8(%rbp)
    movl    -20(%rbp), %eax
    cltq
    movl    -16(%rbp,%rax,4), %eax
    popq    %rbp
    ret
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
    pushq   %rbp
    movq    %rsp, %rbp
    subq    $16, %rsp
    movl    $1, -4(%rbp)
    movl    -4(%rbp), %eax
    movl    %eax, %edi
    call    _Z3fooi
    movl    %eax, -8(%rbp)
    movl    -8(%rbp), %edx
    movl    -4(%rbp), %eax
    movl    %eax, %esi
    movl    $.LC0, %edi
    movl    $0, %eax
    call    printf
    movl    $0, %eax
    leave
    ret
.LFE1:
    .size   main, .-main
    .ident  "GCC: (GNU) 4.8.5 20150623 (Red Hat 4.8.5-4)"
    .section    .note.GNU-stack,"",@progbits  
```

`test.s`中
1. 第7行保存了上一个栈帧指针，第8行设置了当前的栈帧指针，`main`函数在第28、29行做了同样的事情
2. `main`函数第34行的代码实际等价于
```
  pushq %rip
  jmp _Z3fooi
```
3. `foo`函数第17行的代码实际等价于
```
  popq %rip
```
4. `foo`函数第16行将栈帧指针恢复，`main`函数第43行的代码实际等价于
```
  movq %rbp, %rsp
  popq %rbp
```

###### 编译器优化后

```shell
gcc -O3 -S test.cpp
```

使用带有-O3优化选项的命令行生成汇编代码

```
    .file   "test.cpp"
    .text
    .p2align 4,,15
    .globl  _Z3fooi
    .type   _Z3fooi, @function
_Z3fooi:
.LFB12:
    movslq  %edi, %rdi
    movl    $1, -24(%rsp)
    movl    $3, -20(%rsp)
    movl    $5, -16(%rsp)
    movl    -24(%rsp,%rdi,4), %eax
    ret
.LFE12:
    .size   _Z3fooi, .-_Z3fooi
    .section    .rodata.str1.1,"aMS",@progbits,1
.LC0:
    .string "i=%d,j=%d\n"
    .section    .text.startup,"ax",@progbits
    .p2align 4,,15
    .globl  main
    .type   main, @function
main:
.LFB13:
    subq    $8, %rsp
    movl    $3, %edx
    movl    $1, %esi
    movl    $.LC0, %edi
    xorl    %eax, %eax
    call    printf
    xorl    %eax, %eax
    addq    $8, %rsp
    ret
.LFE13:
    .size   main, .-main
    .ident  "GCC: (GNU) 4.8.5 20150623 (Red Hat 4.8.5-4)"
    .section    .note.GNU-stack,"",@progbits 
```
