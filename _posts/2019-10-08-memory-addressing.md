---
layout: post
title: "《深入理解linux内核》内存寻址"
date: 2019-10-08
author: "Zzc"
header-style: text
catalog: true
tags:
  - 编程
  - linux
  - 内存
---

### 内存地址

<pre>
逻辑地址 => 分段单元 => 线性地址 => 分页单元 => 物理地址
</pre>

- 逻辑地址(logical address)：机器语言指令中用来指定操作数或指令的地址，每个逻辑地址包括一个段(segment)和偏移量(offset)
- 线性地址(linear address)：是一个32位或64位无符号整数，也称虚拟地址(virtual address)
- 物理地址(physical address)：用于内存芯片级寻址，与发送到内存总线的电信号对应

内存控制单元(MMU)通过分段单元(segmentation unit)的硬件电路把逻辑地址转换成线性地址；接着通过分页单元(paging unit)的硬件电路把线性地址转换成物理地址。

### 硬件分段

