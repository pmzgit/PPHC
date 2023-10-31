---
title: 突破编程语言的性能瓶颈
taxonomy:
    category: docs
chapterNumber: 3
---

没有任何编程语言是单纯的“语法集合”，每一种语言都是它背后“运行架构”的外在表现，语言之间的差异本质上是运行架构设计方向的差异。

运行架构是指编程语言的设计者在设计语言时，为了实现某种功能或者解决某种问题，所采用的一种算法和数据结构的组合。这种组合决定了编程语言的性能、可扩展性和易用性等方面的特征。

例如，C++ 是一种静态类型的编译型语言，它的运行架构就是操作系统暴露出的进程、线程、系统调用等基本能力，包括了内存管理、指针操作等底层机制，这使得 C++ 能够进行高性能计算和系统编程。而 Python 是一种动态类型的解释型语言，它的运行架构是在一个解释器中解释执行字节码，所以他能实现垃圾回收、动态类型检查等高级特性，这使得 Python 非常适合进行快速原型设计和数据分析。

因此，选择一种编程语言时，最重要的其实是考虑它的运行架构是否符合你的需求。