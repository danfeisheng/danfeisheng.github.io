---
layout: post
title: "多线程-CountdownLatch和CyclicBarrier"
date: "2019-07-08 22:30"
author: "Danfei"
---
2019-07-08 22:30

CountDownLatch

CountDownLatch会导致一条或多条线程在“门口”一直等待，直到另一条线程打开这扇门，线程才得以继续执行。它是由一个计数器变量和两个操作组成的，这两个操作分别
是“导致线程等待直到计数变为0”以及“递减计数变量”

CyclicBarrier

CyclicBarrier允许一组线程彼此互相等待，直到抵达某个公共的屏障点。因为该屏障在等待线程被释放之后可以重用，所以称之为可循环使用的屏障。

Semaphore

Semaphore（信号量）维护了一组许可证，以约束访问被限制资源的线程数。当没有可用许可证时，线程的获取尝试会一直阻塞，直到其他线程释放一个许可证。
Semaphore(int permits)构造函数初始化一个信号量，其中permits指定了可用许可证的数量。这个信号量的公平策略被设置为false（不公平）。或者可以使用
Semaphore(int permits, boolean fair)构造函数把信号量的公平策略设置为ture（公平），当公平策略为false时，信号量不会保证线程获取许可证的顺序，其实
就是抢占式的。当公平策略设置为true时，信号量能保证调用acquire()方法的任意线程能按照方法被调用的顺序获取许可证（先进先出，FIFO）。当然，不限时的
tryAcquire()方法不会遵循公平策略，它们会获取任务可用的许可证。

一般来讲，信号量通常用来控制资源访问，它应当初始化成公平的，从而保证不会有线程被饿死，当针对其他同步控制使用信号量的时候，不公平策略带来的吞吐量好处
是超过公平策略的。

Phaser

Phaser是一个更加弹性的同步屏障，和同步屏障（CylicBarrier）一样，一个phaser使得一组线程在屏障上等待，在最后一条线程到达之后，这些线程得以继续执行，
phaser也提供barrier action的等价操作。和同步屏障协调固定数目的线程不同，一个phaser能够协调不定数目的线程，这些线程可以在任何时候注册。为了实现这一
功能，phaser使用了phase和phase值。