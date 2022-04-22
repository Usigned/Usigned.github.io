---
title: python并发编程
tags: [python, 操作系统]
---

基本上是阅读python标准库threading和multiprocessing的阅读记录

官方文档：

1. [threading — Thread-based parallelism — Python 3.10.4 documentation](https://docs.python.org/3/library/threading.html)
2. [multiprocessing — Process-based parallelism — Python 3.10.4 documentation](https://docs.python.org/3/library/multiprocessing.html)

## CPython GIL

在CPython中，由于全局解释器锁的存在，同时只有一个线程能在运行（就算有多个cpu核心）。如果想更好利用多核机器，建议使用基于进程的并发。同时，如果任务是I/O密集型的，线程也是一个合适的选择（因为线程在进入I/O时会释放GIL）。

## Python线程模型

在threading文档中有提到python的线程模型是参考java的线程模型实现的，只不过在Java中锁和条件变量是对象的固有属性（每个对象都有自己的锁和条件变量），而在python中其是单独的对象。其功能仅是Java线程功能的一个子集，python中线程不支持打断，停止等操作。

> The design of this module is loosely based on Java’s threading model. However, where Java makes locks and condition variables basic behavior of every object, they are separate objects in Python. Python’s [`Thread`](https://docs.python.org/3/library/threading.html#threading.Thread) class supports a subset of the behavior of Java’s Thread class; currently, there are no priorities, no thread groups, and threads cannot be destroyed, stopped, suspended, resumed, or interrupted. The static methods of Java’s Thread class, when implemented, are mapped to module-level functions.

## python同步原语

> 虽然threading和multiprocessing中都提供了api相同的同步原语，但这部分介绍的详细内容在threading中。下面说明中的线程，都可以替换为进程

1. Lock（原始锁）
   - 目前python中最底层的同步原语
   - 没有归属线程：任何线程都可以调用release方法
2. RLock（可重入锁）
   - 有所属进程/线程和递归等级
     - 什么是递归等级？
   - 只能由其所属的线程release
   - 可重入：拥有该锁的线程再次获取该锁时不用重新获取
     - 在Java中好像可以解决使用lock带来的死锁问题，python里lock没有归属进程，所以应该不会存在此问题，仅仅是优化了同线程不可重用的问题
3. Condtion Variable (wait/notify)
   - 本质上是对锁的封装（默认使用RLock）
   - 允许一个或多个进程在被其他线程通知前等待
   - 使用Condition Variable和直接使用锁的区别？
4. Semaphore/Bounded Semaphore（信号量）
   - 内部管理一个原子计数器
   - 常用于保护有数量限制的资源，如数据库连接等

5. Event

   - 内部维护一个Flag，只有true/false两种状态

   - 基本上相当于一个max=1的bounded semaphore

## Python多进程

### 进程间通信

1. Queue（队列）
   - 基本上是queue.Queue的拷贝
   - 线程和进程安全
   - 由一个pipe和一些锁/信号量实现
2. Pipes （管道）
   - 一对连接对象（管道两端），默认全双工
   - 如果多个进程从管道的**同一端**写入/读取数据，数据可能会被污染。从不同端读取则没有问题。

### 进程间共享状态

> 文档指出：在并发编程时，尤其使用多进程时，应该避免使用共享状态？

1. 共享内存
   - 默认情况下只支持特定的数据结构（Value/Array）

2. server进程
   - 启动一个服务进程用于保存对象，其他进程使用代理操作
   - 相较于共享内存更灵活
     - 支持任意的数据结构，且能够跨网络
     - 但是比共享内存慢

### 进程池

> 我发现到处都有池的概念，什么线程池、进程池，数据库的连接池啊。感觉他们的思想就是：创建对象消耗太大了，用完就销毁有些浪费，不如存起来下次再用这种。

感觉没啥好写的，具体参加multiprocess文档吧。

## 总结

并发编程这一块水感觉还是蛮深的，涉及语言本身和操作系统。最主要平时写代码也没用过（最多调调dataloader的num_workers参数），面试被问题来就发虚，不过问了下周围做项目的同学，他们平时好像也没怎么用过😂。
