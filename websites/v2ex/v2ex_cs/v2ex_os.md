
各位吴彦祖，有推荐的 Kernel 相关的书籍推荐吗？ https://www.v2ex.com/t/798111
- > 不知道你想学什么以及到什么阶段了，general 来说很推荐 MIT 6.S081 https://pdos.csail.mit.edu/6.S081/2020/schedule.html 把 lab 刷完可以学到 syscall, memory allocation, multi threading, file system, network 相关的基础知识
- > 想了解 Kernel Module 开发的话，也可以看看 The Linux Kernel Module Programming Guide https://github.com/sysprog21/lkmpg
- > 有一本入门书，Linux 内核设计与实现，书不厚，记得 200 多页，让你对内核常见模块有个认识。如果想深入，建议直接看代码，编译代码调试，观察
- > 如果非常喜欢内核的话，推荐两本厚书，讲得比较透：linux 内核完全注释-0.11 版，深入 linux 架构

The Linux Kernel Module Programming Guide 升级至 5.0 版并开源！ https://www.v2ex.com/t/797371
```console
台湾成功大学助理教授 黄敬群 (Jim Huang, jserv) 近期将 The Linux Kernel Module Programming Guide (Linux 内核模块编程指南) (针对 Linux Kernel 2.6) 
的内容升级到最新的 kernel versions (v5.x)。

并且大方的开源 LaTeX 、PDF 至 GitHub 上，欢迎对 Linux 内核感兴趣的伙伴们，可以阅读学习。GitHub 项目连结 
https://github.com/sysprog21/lkmpg
```
- > 可以參考下
  * > https://github.com/sysprog21/lkmpg/tree/master/examples
  * > https://github.com/sysprog21/lkmpg/blob/master/lkmpg.tex

Linux 环境编程有什么经典教材吗 https://www.v2ex.com/t/761355
- > 如果不写驱动和内核，看《 The Linux Programming Interface 》这一本就够了，虽然这一本也还是有点老，但是写 Linux 下的应用也够了。
- > 简短的就 APUE 。大部头的就 TLPI，1500 页
- > apue 只讲 posix，tlpi 除了 posix 的部分还要讲 linux 特有的部分，算是 apue 的超集吧。你可以先看 apue 再看 tlpi，不冲突的

首届 eBPF.io Summit 2020 见闻 https://www.v2ex.com/t/720992
- > Linux 内核之旅开源社区（ http://kerneltravel.net ，西安邮电大学陈莉君老师创办）在 eBPF 方面做了很多研究，还有一个 ebpf 的开源项目，有兴趣的可以去看看，顺带给个 star，谢谢~
<br> https://github.com/linuxkerneltravel/lmp

内核态用户态的数据拷贝,有点不明白 https://www.v2ex.com/t/687113
