---
title: Java ReentrantLock
date: 2020-04-30 20:12:55
categories: 
- Java
tags:
- 锁
---

# Java ReentrantLock

## ReentrantLock描述

ReentrantLock是一个可重入的排它锁，ReentrantLock会被最后一次成功得锁的线程所持有，在其没有被任何一个线程所持有时调用lock方法的线程可成功得锁。

其提供了一个参数sync类型为Sync继承自AbstractQueuedSynchronizer，来记录当前ReentrantLock是公平锁还是非公平锁

- 公平锁

  进程得锁的顺序将按照调用lock方法的顺序

- 非公平锁

  进程将通过竞争的方式得锁

## ReentrantLock得锁放锁过程

### 得锁

lock被调用时会调用sync的lock方法

- 首先通过tryAcquire尝试得锁，成功则不做任何操作

  AbstractQueuedSynchronizer有一个Thread变量来记录当前得锁的线程。

  tryAcquire方法会获取state（抽象概念的锁）来并判断当前独占变量是否被线程所占有，以及锁被重入的次数

  - 若未被占用则调用hasQueuedPredecessors探测阻塞队列中没有先来的线程等待得锁（公平锁非公平锁判断方式不同），当前线程得锁，返回true
  - 若已被占用则判断得锁线程是否为当前线程，如果使的话则记录state值加一，返回true
  - 若上述都不符合标识得锁失败返回false

- 如果得锁失败则为当前线程创建一个结点并加入等待队列。

  - 使用addWaiter调用native方法将代表当前线程的结点采用CAS的方式插入队尾，让后调用acpuireQueued阻塞并等待得锁

### 放锁

unlock放锁时会调用sync的release，参数为1

- 放锁release方法调用tryRelease方法尝试释放独占变量并返回一个值标识该锁是否被完全释放

  - tryRelease会获取state并将其值减一，如果减一后等于零则表明该锁被完全释放，并将持有锁的线程设为null

  - 若锁完全释放则会判断当前等待队列是否有阻塞线程，有则会调用unparkSuccessor唤醒一个结点所代表的线程并返回true，否则返回false