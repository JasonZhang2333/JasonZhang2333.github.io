---
layout: post
title: "《深入Linux内核架构》内存"
date: 2019-10-10
author: "Zzc"
header-style: text
catalog: true
tags:
  - 编程
  - 操作系统
  - Linux
  - 内存管理
  - 笔记
---

> 本文基于《深入理解Linux内核架构》第三章，`Linux`为`2.6.24`版本。

### NUMA

###### NUMA内存组织

![img](/img/in-post/post-memory-management/201910101521.jpg)
*NUMA架构*

早前计算机架构采用一致内存访问(uniform memory access, `UMA`)方式管理物理内存，所有CPU通过北桥访问内存，共享同一总线，每个CPU访问内存的速度同样快；随着CPU数量的增加，北桥成为性能瓶颈，逐渐改为采用非一致内存访问(non-uniform memory access, `NUMA`)的方式管理物理内存，每个CPU有自己的本地内存，访问更快(Local Access)，各个CPU通过总线连接起来以支持对其他CPU本地内存的访问(Remote Access)，但是比访问自身本地内存慢，如上图所示。

![img](/img/in-post/post-memory-management/201910101625.png)
*NUMA内存划分*

- 内存划分为`Node`，每个`Node`关联到系统中的一个CPU，`Node`在内核中表示为数据结构`pg_data_t`的实例
- 各个`Node`又划分为`Zone`，每个`Node`包含3个`Zone`，

https://www.ibm.com/developerworks/cn/linux/l-numa/index.html

###### NUMA查询

```shell
lscpu   # linux下查看机器NUMA拓扑结构
```

```
[zhichen.zhang@localhost ~]$ lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                24     //24个thread
On-line CPU(s) list:   0-23
Thread(s) per core:    1      //每个core有1个thread
Core(s) per socket:    12     //每个socket有12个core
Socket(s):             2      //有两个socket（插槽）
NUMA node(s):          2      //有两个NUMA node
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 85
Model name:            Intel(R) Xeon(R) Gold 6146 CPU @ 3.20GHz
Stepping:              4
CPU MHz:               3288.058
BogoMIPS:              6581.35
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              1024K
L3 cache:              25344K
NUMA node0 CPU(s):     0-11
NUMA node1 CPU(s):     12-23
```

