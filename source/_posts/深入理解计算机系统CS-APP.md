---
title: '深入理解计算机系统CS:APP'
date: 2020-10-21 22:12:17
tags:
- 操作系统
- CSAPP
---

深入理解计算机系统CS:APP是我非常喜欢的一本介绍计算机系统的书籍。他补充了我许多计算机基础知识，例如：编译，汇编，二进制补码，CPU流水线，高级语言的汇编优化，内存管理等等，很多计算机基础，都在这本书中涉及。他的阅读体验非常好，每节后面都有练习题并配有讲解答案，非常适合边学边练。

这里我针对每节的阅读，摘录我感兴趣的部分，并加以学习和拓展。当然，我不是第一次阅读了，说起来惭愧，每次想重读本书时，都不能坚持，这里我不想再拖了，我一定要坚持完成他！

<!--more-->

# 序言导论学习

- 现在一般认为问题抽象、系统抽象和数据抽象是计算机类专业毕业生的核心能力！--- 《CSAPP:IX页中文版序二》

    作序者从软件角度给出了见解，说的非常的好，这也是序者作为软件工程领域顶尖人士的看法。

    我现在的认知考虑（2020-10-21），我认为，不能在做脱离实际的编程了，一定要了解计算机硬件与其体系结构，不管是当前中国计算机技术的环境，还是计算机技术在生活中的普及。都在告诉我们，不了解计算机硬件，结构，体系等诸多计算机技术，是搞不好计算机的，尤其是近些年来中国的芯片产业不断被打压，也正是由于”造不如买“等诸多听着有理，实际却非常错误的观念造成的，通过芯片，美国人告诉你，没有芯片这个硬通货，你软件写的再妙又有什么用？

    所以，我认为，当下计算机硬件，体系构造等领域急需大量人才，以芯片领域为例：就需要大量的物理学家（电路设计），化学家（电路刻蚀），数学家（逻辑设计）以及计算机科学家。所以学习计算机硬件，体系等非常重要！

- 深入研究方向：编译器、计算机体系结构、操作系统、嵌入式系统、网络互联和网络安全

    编译器：《编译原理》

    计算机体系结构：《计算机组成与设计：硬件/软件接口》、《计算机体系结构：量化研究方法》

    操作系统：《深入理解计算机系统：CSAPP》

    网络互联和网络安全：《计算机网络：自顶向下方法》、《TCP/IP详解》

- x86-64是自**1978**年起，以8086微处理器为代表，不断进化的最新成果。

    为什么8086在中国的大学组成原理书中不断被提及学习，因为他是现代计算机芯片的鼻祖。x86 是英特尔微处理器产品线的命名规则，64是指其寻址长度达到64位，这也是我们有时候下载软件或者Linux系统时，会出现选择x86版本（32位版本）或者x86-64版本（64位版本）的区别。

- 了解一个c程序的生命周期，通过这个周期扩展了解计算机系统的主要概念和主题。 ---《CSAPP：XIV本书概述-第1章》

    c，c++，java等编译型语言最重要的地方就在于他们是要编译的，与JavaScript，python等解释型语言相比。编译型不像解释型一样，由解释器将代码一句一句运行，而是以编译器，先将代码编译为机器代码，再加以运行。

- 不能用表达式( x - y < 0) 来代替 (x < y)，因为前者可能会产生溢出；也不能用表达式(-y < -x) 来代替，因为补码表示中负数和正数的范围是不对称的。---《CSAPP：XIV本书概述-第2章》

    这里需要学习计算机中数字的二进制表示，为什么会溢出？正数和负数在计算机中如何表示？他们如何计算？他们的取值范围是什么？这些都在第2章中将被解析。

- 程序在计算机中的机器级表示。栈和寄存器的使用，不同控制结构（条件，循环，递归等）生成的汇编指令，通过这里可以懂得程序在机器上是如何表示的。 ---《CSAPP：XIV本书概述-第3章》

- 基本的组合和时序逻辑元素，并展示这些元素如何在数据通路中组合到一起。---《CSAPP：XIV本书概述-第4章》

    一个最重要的概念：**流水线（Pipeline）**是一个非常好的设计理念，不管是在计算机中还是工厂中，运用非常广泛，也非常的有效率，他将CUP的处理效率提升了几倍！

- 让程序员通过使编译器能够生成更有效的机器代码来学习编写C代码。你会惊讶于C代码做的一些简单变换能给程序带来多大的速度提升！---《CSAPP：XIV本书概述-第5章》

    你可能没想过，C语言一些简单的如：调整代码顺序，简单的增加几句就可以提升代码速度，这是很神奇的一件事情！

- 存储器是由不同容量、造价和访问时间的存储设备组成的**层次结构**。---《CSAPP：XIV本书概述-第6章》

    **存储器的层次结构**在计算机中是一个非常重要的概念，从寄存器，CPU高速缓存，内存，ssd，硬盘都是层次结构中的一员，他们共同贡献自己的力量，让你感觉计算机的存储既快速又宽阔。

- 连接器生成的目标文件是与一些像加载、虚拟内存和内存映射这样的概念相关的。---《CSAPP：XIV本书概述-第7章》

- **进程**的基本概念，进程是对一个正在执行的程序的一种抽象。---《CSAPP：XIV本书概述-第8章》

    **进程**是非常重要的，一个进程可以说就是一个应用程序，它包含了虚拟内存，进程管理，并发性等概念，可以说操作系统下就是进程了。

- **虚拟内存**，为什么不同的并发进程各自都有一个完全相同的地址范围，能共享某些页，而又独占另一些页。计算机是如何做到让每一个进程看到的内存都是一样连续和可用的，那就是虚拟内存！---《CSAPP：XIV本书概述-第9章》

- 系统级I/O ---《CSAPP：XIV本书概述-第10章》

- 网络编程 ---《CSAPP：XIV本书概述-第11章》

    Socket套接字编程，Java有很好的实现，并且可以选择TCP和UDP可靠和不可靠传输协议，可以通过Java来了解。

- 并发编程，**三种编写并发程序的基本机制（进程、IO多路复用、线程）**；探讨了用P、V信号量操作来实现同步、线程安全和可重入、竞争条件以及死锁等的基本原则。---《CSAPP：XIV本书概述-第12章》

    **进程，线程，PV信号量**这三个是非常重要的概念，进程 > 线程，所以，经常可以看到，进程中存在线程的并行等操作。
    
- 本书虽然谈及了机器语言，但是重点并不在于如何手工编写汇编语言，而是关注C语言编译器是如何将C语言的结构翻译成机器代码的，包括编译器是如何翻译指针、循环、过程调用以及开关（switch）语句的。更进一步地，我们将更广泛和全盘地看待系统，包括硬件和系统软件，涵盖了包括链接、加载、进程、信号、性能优化、虚拟内存、I/O以及网络与并发编程等在内的主题。