---
layout: post
title: "《深入Linux内核架构》进程"
date: 2019-10-23
author: "Zzc"
header-style: text
catalog: true
tags:
  - 编程
  - 操作系统
  - Linux
  - 进程管理
  - 笔记
---

> 本文基于《深入理解Linux内核架构》第二章，`Linux`为`2.6.24`版本。

内核需要处理进程相关的两个任务：
1. 为各个进程分配多长时间，何时切换到下一进程，这取决于调度器策略，与平台无关
2. 在进程切换后，必须保证切换到进程的执行环境与之前完全相同，这与处理器极度相关，需要汇编实现

### 优先级与生命周期

![img](/img/in-post/post-process-management/201910231734.png)

*通过时间片分配CPU时间*

进程按照时间片调度，分配的时间片份额和进程相对重要性相当，这种方案称为抢占式多任务处理(preemptive multitasking)，被抢占进程的所有寄存器内容和页表都会被保存。这一简化模型忽略了几个细节，首先CPU为了利益最大化，不会立即执行无事可做的进程，其次`Linux`支持不同的调度类别，如完全公平的调度和实时调度，所以当重要的进程可以运行时，可能会抢占当前进程。

![img](/img/in-post/post-process-management/201910231754.png)

*进程状态转换*

进程可能有以下几种状态：
- **运行**：该程序此刻正在执行
- **等待**：进程就绪等待执行
- **睡眠**：进程正在等待外部事件无法运行，调度器无法在切换时选择该进程
- **终止**：进程执行终止

系统将所有进程保存在一个进程表中，睡眠进程会特别标记出来分类到若干队列，在外部事件发生时唤醒；有一种特殊的僵尸状态，在`UNIX`操作系统下，进程终止必须首先由另一个进程或者用户杀死（通常是发送`SIGTERM`或`SIGKILL`），然后进程父进程在子进程终止后调用`wait4`系统调用，该系统调用使得内核可以释放为子进程保留的资源，当子进程终止而父进程未调用`wait4`时就会形成僵尸进程，僵尸进程耗费系统资源极少。

`Linux`进程管理结构还记录了另外两种进程状态选项：**用户态**和**内核态**，从用户态切换内核态有两种方式：
- 通过系统调用在受控情况下实现，这种访问经由明确的路径定义
- 通过中断触发，中断不可预测且与通常当前执行进程无关，例如网络数据包进入系统是通过中断

内核抢占调度模型的层次结构：
- 普通进程总是可能被抢占，当一个重要进程状态变为可运行时，调度器可以决定是否立即执行该进程
- 系统处于核心态并在处理系统调用时不可被抢占，但是中断可以中止系统调用
- 中断拥有最高优先级，可以暂停其他进程，因为中断触发后需要尽快处理

### 进程表示

`Linux`内核涉及进程的算法都围绕一个名为`task_struct`的数据结构建立，该结构定义在`include/sched.h`中，如下所示：

```cpp
struct task_struct {
  volatile long state; /* -1表示不可运行，0表示可运行，>0表示停止 */
  void *stack;
  atomic_t usage;
  unsigned long flags; /* 每进程标志，下文定义 */
  unsigned long ptrace;
  int lock_depth; /* 大内核锁深度 */

  int prio, static_prio, normal_prio;
  struct list_head run_list;
  const struct sched_class *sched_class;
  struct sched_entity se;

  unsigned short ioprio;

  unsigned long policy;
  cpumask_t cpus_allowed;
  unsigned int time_slice;

#if defined(CONFIG_SCHEDSTATS) || defined(CONFIG_TASK_DELAY_ACCT)
  struct sched_info sched_info;
#endif

  struct list_head tasks;
  /* ptrace_list/ptrace_children链表是ptrace能够看到的当前进程的子进程列表 */
  struct list_head ptrace_children;
  struct list_head ptrace_list;
  struct mm_struct *mm, *active_mm;

/* 进程状态 */
  struct linux_binfmt *binfmt;
  long exit_state;
  int exit_code, exit_signal;
  int pdeath_signal; /* 在父进程终止时发送的信号 */
  unsigned int personality;
  unsigned did_exec:1;
  pid_t pid;
  pid_t tgid;
  struct task_struct *real_parent; /* 真正的父进程（在被调试的情况下） */
  struct task_truct *parent; /* 父进程 */
  /* children/sibling链表外加当前调试的进程，构成了当前进程的所有子进程 */
  struct list_head children; /* 子进程链表 */
  struct list_head sibling; /* 连接到父进程的子进程链表 */
  struct task_struct *group_leader; /* 线程组组长 */
  /* PID与PID散列表的联系 */
  struct pid_link pids[PIDTYPE_MAX];
  struct list_head thread_group;
  
  struct completion *vfork_done; /* 用于vfork() */
  int __user *set_child_tid; /* CLONE_CHILD_SETTID */
  int __user *clear_child_tid; /* CLONE_CHILD_CLEARTID */
  
  unsigned long rt_priority;
  cputime_t utime, stime, utimescaled, stimescaled;
  unsigned long nvcsw, nivcsw; /* 上下文切换计数 */
  struct timespec start_time; /* 单调时间 */
  struct timespec real_start_time; /* 启动以来的时间 */
  /* 内存管理器失效和页交换信息 */
  unsigned long min_flt, maj_flt;
  
  cputime_t it_prof_expire, it_virt_expires;
  unsigned long long it_sched_expires;
  struct list_head cpu_timers[3];
  
/* 进程身份凭据 */
  uid_t uid, euid, suid, fsuid;
  gid_t gid, egid, sgid, fsgid;
  struct group_info *group_info;
  kernel_cap_t cap_effective, cap_inheritable, cap_permitted;
  
  unsigned keep_capabilities:1;
  struct user_struct *user;
  
  /* 出去路径后的可执行文件名，可用[gs]et_task_comm访问，通常由flush_old_exec初始化 */
  char comm[TASK_COMM_LEN];
  
/* 文件系统信息 */
  int link_count, total_link_count;
/* ipc相关 */
  struct sysv_sem sysvsem;
/* 当前进程特定于CPU的状态信息 */
  struct thread_struct *fs;
/* 文件系统信息 */
  struct fs_struct *fs;
/* 打开文件信息 */
  struct files_struct *files;
/* 命名空间 */
  struct nsproxy *nsproxy;
/* 信号处理程序 */
  struct signal_struct *signal;
  struct sighand_struct *sighand;
  
  sigset_t blocked, real_blocked;
  sigset_t saved_sigmask; /* 用TIF_RESTORE_SIGMASK恢复 */
  struct sigpending pending;
  
  unsigned long sas_ss_sp;
  size_t long asa_ss_sp;
  int (*notifier)(void *priv);
  void *notifier_data;
  sigset_t *notifier_mask;
  
#ifdef CONFIG_SECURITY
  void *security;
#endif

/* 线程组跟踪 */
  u32 parent_exec_id;
  u32 self_exec_id;
  
/* 日志文件系统 */
  void *journal_info;
  
/* 虚拟内存状态 */
  struct reclaim_state *reclaim_state;
  struct backing_dev_info *backing_dev_info;
  struct io_context *io_context;
  unsigned long ptrace_message;
  siginfo_t *last_siginfo; /* 由ptrace使用 */
...
};
```

`task_struct`内容可以分解为几个部分：
- 状态和执行信息，如信号、二进制格式、`pid`、到父进程及其他进程的指针、优先级和程序执行有关的时间信息等
- 已经分配的虚拟内存的信息
- 进程身份凭据，如用户ID、组ID和权限等
- 包含程序代码的二进制文件以及进程处理的文件的文件系统信息
- 
