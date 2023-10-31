---
title: 面试题
taxonomy:
    category: docs
---

#### No.08：Node.js 是如何在单线程内实现非阻塞 I/O 的？

Node.js 借助 libuv 的能力，利用队列和线程池实现了非阻塞 I/O。

首先 Google V8 引擎负责单线程解释执行 JavaScript 代码，在遇到网络或者磁盘 I/O 请求后，运行模式如下：

1. 这个 I/O 请求会被入队列，同时注册一个回调函数，等待 I/O 结果返回以后的回调函数调用
2. 让出主线程，让 V8 继续执行其他 js 代码
3. libuv 开始处理队列任务
    1. windows 系统下，通过 IOCP 组件执行真正的异步 I/O（其实 IOCP 在内核内部依然是线程池实现的）
    2. 由于 Linux 下的 socket 只能阻塞运行，所以是采用的用户态线程池技术 + epoll 实现的非阻塞 I/O
4. 某个队列任务拿到返回结果后，开始在 V8 主线程排队等待执行回调函数
5. 排到自己以后，继续执行该请求的后续代码

Node.js 的核心技术有两个：V8 提供的天然单线程排队执行机制，和 libuv 提供的跨越 Linux、Windows、macOS 三个系统的非阻塞 I/O 能力。

#### No.09：Go 语言为什么快？

Go 语言之所以快，主要是因为其并发模型、垃圾回收、编译优化、内存管理和标准库等方面都是完全面向“高性能网络编程”这个需求设计的。

Go 在语言 runtime 层面实现了完善的用户态协程调度，并且将以 epoll 为代表的 I/O 多路复用贯彻到了网络的方方面面，编码难度低，很容易就能写出高性能的代码。具体来说，Go 语言在设计上有如下几个优势：

1. 并发模型：基于 CSP（Communicating Sequential Processes）理论，使用 goroutines 和 channels 达成了高并发任务的高效率调度和处理。
2. 垃圾回收：并发垃圾回收机制，提高垃圾回收效率，使用写屏障技术减少锁使用，提高程序性能。
3. 编译优化：编译器在编译阶段进行多种优化操作，减少资源消耗，提高运行速度。
4. 内存管理：自动内存管理，减轻程序员负担，减少内存泄漏等问题。
5. 标准库：丰富的标准库提供高度优化的函数和类型，提高开发效率，保证程序性能。


#### No.10：Goroutine 是如何实现高性能的？

传统的进程/线程之所以慢，是因为当某个 CPU 核心这个图灵机从执行一个线程切换为执行下一个线程的时候，由于会出现用户态->内核态->用户态的切换（即上下文切换），导致了对内存的读写。Go 在语言 runtime 层面实现了一个可以在用户态的单个线程的内部进行自主管理的协程调度机制，让一个协程的代码执行完之后，不需要进行上下文切换，无需读写内存，在操作系统看来，这个线程没有任何的变化，依然在按照顺序执行一个又一个指令。这个机制使得 CPU 时间片得到了最大程度的利用，是 Goroutine 高性能最大的贡献者。

详细地说，Goroutine 拥有如下这些专门的设计来达成超高性能：

1. 并发执行：Goroutine 可以在多个 CPU 核心上并发执行，充分利用多核处理器的性能。通过在操作系统层面进行调度，Goroutine 可以在不同的核上运行，从而实现真正的并行计算。
2. 栈空间：Goroutine 的栈空间非常小，通常只有 2KB 左右。这使得大量的 Goroutine 可以在内存中同时存在，而不会因为栈空间的分配和回收导致性能下降。
3. 上下文切换开销小：Goroutine 的上下文切换开销非常小，因为它们在同一个操作系统线程中运行。当一个 Goroutine 阻塞时，其他 Goroutine 可以继续执行，而不需要等待阻塞的 Goroutine 完成。这避免了传统线程模型中的忙等待问题，提高了程序的执行效率。
4. 简单易用：Goroutine 的使用非常简单，只需要在函数调用前加上关键字 `go` 即可创建一个 Goroutine。这使得开发者可以轻松地编写高并发的代码，而不需要关心复杂的线程同步和互斥问题。
5. 内置调度器：Go 语言内置了一个调度器（scheduler），负责对 Goroutine 进行调度和管理。调度器使用了一种称为 M:N 调度的技术，将多个 Goroutine 分配到多个操作系统线程上执行。这种调度策略可以在保证程序执行顺序的同时，最大限度地利用多核 CPU 的全部计算资源。