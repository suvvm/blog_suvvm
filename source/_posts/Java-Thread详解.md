---
title: Java Thread详解
date: 2020-04-12 17:52:27
categories: 
- Java
tags:
- 线程
---

# Java Thread详解

## 线程状态

Thread.State有6种状态

- NEW
  - 新生态
- RUNNABLE
  - 就绪态
  - 运行态
- BLOCKED
  - 阻塞态（wait IO阻塞）
- WAITING
  - 阻塞态（join 等阻塞）
- TIMED_WITING
  - 阻塞态（sleep 指定时间等待）
- TERMINATED
  - 销毁态

## 线程的优先级

Thread.priority代表线程的优先级，范围从1到10.

- Thread.MIN_PRIORITY = 1

  代表线程的最低优先级

- Thread.MAX_PRIORITY = 10

  代表线程的最高优先级

- Thread.NORM_PRIORITY = 5

  代表线程的通常优先级

优先级不是绝对的，只影响获得处理机的概率与时间片长短。

## 用户线程与守护线程

默认所有线程都是用户线程，在线程启动前使用setDaemon(true)将线程设为守护线程。

- 守护线程为用户线程服务
- JVM确保用户线程执行完毕
- JVM不需要等待守护线程执行完毕

## 常用方法

- yield

  用于礼让，使当前线程转为就绪态

- sleep

  将当前线程阻塞指定时间（state转为TIME_WATING）

- join

  执行目标线程，阻塞当前线程直至目标线程执行完毕

- 无参wait

  多与synchronized一同使用，等价于wait(0)，使当前线程阻塞并放锁直到另一线程在对应对象上调用notify或notifyAll

- wait(long timeout)

  多与synchronized一同使用，使当前线程阻塞并放锁直到另一线程在对应对象上调用notify或notifyAll，或是指定的时间超时

- notify

  唤醒等待该对象的**一个**线程，线程选择取决于调度算法，用户无法干涉

- notifyAll

  唤醒等待该对象的所有线程

- interrupt

  用于中断线程，将中断标志设为true，其他操作由用户程序决定

- interrupted

  探测线程中断标志状态，若为true则清除中断标志并返回true，否则返回false

- isInterrupted

  探测线程中断标志状态，不影响中断标志位

- setPriority

  设置线程优先级

- setDaemon

  设置线程为守护线程

- currentThread

  获取当前正在运行的线程对象（获取自身）

- isAlive

  判断线程是否存活

- setName、getName

  设置、获取线程代理对象名称

## 线程的同步

每个对象都有一个排它锁

synchronized可以锁定具体对象，若标识在成员变量上则锁的是this，若标识在静态变量上锁的则是该类在内存中的模板。

synchronized方法，若执行synchronized方法必须获得调用该方法的对象(this)的锁才可执行，否则所属线程阻塞，方法执行后独占锁直到方法返回。

synchornized(obj){}块，锁定指定的同步监视器obj，执行syhchornized块必须获得同步监视器的锁，否才所属线程阻塞，执行时独占同步监视器锁直至synchronized块执行完毕。

## 实现多线程与同步示例

[Java多线程的实现](https://www.suvvm.work/2020/03/15/Java多线程的实现/)