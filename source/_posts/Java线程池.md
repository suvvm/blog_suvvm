---
title: Java线程池
date: 2020-04-01 18:07:44
categories: 
- Java
tags:
- 线程
---

# Java线程池

线程池用于管理线程，任务运行前从线程池中取出线程，任务执行完毕后将线程放回线程池，还维护了一些基本的统计信息，如已完成的任务数量。线程池的存在减少了创建于销毁线程所带来的开销。
## 应用场景
由于构建一个新的线程涉及到与OS的交互，是有一定代价的。如果Java程序中创建了大量生命周期较短的线程，应该使用线程池。

## 线程池的种类
Exector类中提供了多种静态工厂方法来构造线程池
- newCachedThreadPool
	创建一个不限制线程数量上限的线程池，空闲线程会被保留60秒
- newFixedThreadPool
	创建一个线程数固定的线程池，空闲线程将一直被保留
- newSingleThreadExecutor
	创建只有一个线程的线程池，该线程池顺序执行提交的每一个任务
	

以上3种方法都是创建ThreadPoolExecutor实例

- newScheduledThreadPool
	创建一个有计划性运行的线程池，内部有延迟阻塞队列，可以做到在给定延迟后执行或周期性执行任务
- newSingleThreadScheduledExecutor
	创建一个有计划性运行的只有一个线程的线程池，内部有延迟阻塞队列，可以做到在给定延迟后执行或周期性执行任务
	

以上2种方法都是创建ScheduledThreadPoolExecutor实例，ScheduledThreadPoolExecutor继承自ThreadPoolExecutor

- newWorkStealingPool(JDK1.8)
	创建一个抢占式的并行线程池，可以控制线程并发数量，任务的执行是无序的，创建的是ForkJoinPool的实例。
	
## ThreadPoolExecutor

### 参数

- corePoolSize	核心线程池大小
- maximumPoolSize	线程池最大线程数
- keepAliveTime	线程的存活时长，只有当池内线程数量大于corePoolSize时或指定allowCoreThreadTimeOut(true)时生效
- unit keepAliveTime的单位
	- TimeUnit.DAYS;	天
	- TimeUnit.HOURS;	小时
	- TimeUnit.MINUTES;	分钟
	- TimeUnit.SECONDS;	秒
	- TimeUnit.MILLISECONDS;	毫秒
	- TimeUnit.MICROSECONDS;	微妙
	- TimeUnit.NANOSECONDS;	纳秒
- workQueue 阻塞队列，用于存储等待执行的任务
	- ArrayBlockingQueue 数组实现，有界队列
	- LinkedBlockingQueue 链表实现，无界队列
	- SynchronousQueue 同步移交队列，只包含一个元素，妄图插入元素到队列的进程将被阻塞，直到队列中的元素被取出，获取元素同理也会被阻塞。
- threadFactory 创建线程的工厂，默认使用DefaultThreadFactory
- handler 拒绝策略
	- ThreadPoolExecutor.AbortPolicy
	丢弃任务并抛出RejectedExecutionException异常。 
	- ThreadPoolExecutor.DiscardPolicy
	丢弃任务，不抛出异常。 
	- ThreadPoolExecutor.DiscardOldestPolicy
	丢弃队列最年迈的任务，尝试重新执行任务（循环处理）
	- ThreadPoolExecutor.CallerRunsPolicy
	由提交任务的线程处理该任务 
- runState
	- private static final int RUNNING    = -1 << COUNT_BITS;
	创建线程池后，线程池处于运行态
    - private static final int SHUTDOWN   =  0 << COUNT_BITS;
	调用shutdown后的状态，不接收新的任务，执行当前任务直到所有任务执行完毕且阻塞队列被清空后转为TIDYING状态
    - private static final int STOP       =  1 << COUNT_BITS;
	调用shutdownNow方法后的状态，不接收新任务，且会尝试终止当前正在执行的任务，所有任务被终止后转为TIDYING状态
    - private static final int TIDYING    =  2 << COUNT_BITS;
	所有任务都终止的状态此时开始执行terminated方法（默认为空，可覆盖）
    - private static final int TERMINATED =  3 << COUNT_BITS;
	执行完terminated后线程池转为终止状态
	
### 构造器

提供核心线程池大小、最大线程数、存活时间、存活时间单位、阻塞队列构造线程池。

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         Executors.defaultThreadFactory(), defaultHandler);
}
```

提供核心线程池大小、最大线程数、存活时间、存活时间单位、阻塞队列、创建线程的工厂构造线程池。

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         threadFactory, defaultHandler);
}
```

提供核心线程池大小、最大线程数、存活时间、存活时间单位、阻塞队列、拒绝策略构造线程池。

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          RejectedExecutionHandler handler) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
         Executors.defaultThreadFactory(), handler);
}
```

提供核心线程池大小、最大线程数、存活时间、存活时间单位、阻塞队列、创建线程的工厂、拒绝策略构造线程池。

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
        null :
    AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

### 提交任务

- public void execute(Runnable command)

  Exector接口定义，ThreadPoolExecutor实现

  向线程池提交执行一个任务，由线程池核心线程数、最大线程数、阻塞队列状态觉得如何运行这个任务

  ```java
   public void execute(Runnable command) {
       if (command == null)
           throw new NullPointerException();
       	/*
           * Proceed in 3 steps:
           *
           * 1. If fewer than corePoolSize threads are running, try to
           * start a new thread with the given command as its first
           * task.  The call to addWorker atomically checks runState and
           * workerCount, and so prevents false alarms that would add
           * threads when it shouldn't, by returning false.
           *
           * 1. 如果运行的线程少于corePoolSize，将以传入的变量command为第一个任务启动新的线程。
           * addWorker将检查runState和workerCount，
           * 所以可以通过返回false来防止在不该创建线程时报错。
           *
           * 2. If a task can be successfully queued, then we still need
           * to double-check whether we should have added a thread
           * (because existing ones died since last checking) or that
           * the pool shut down since entry into this method. So we
           * recheck state and if necessary roll back the enqueuing if
           * stopped, or start a new thread if there are none.
           *
           * 2. 如果任务可以成功进入阻塞队列，也需要再次检查是否应该添加新的线程
           * （自上吃检查到现在可能由线程已经死亡），因为需要判断线程池是否在进入此方法后被关闭。
           *	所以要重新检查状态，如果已经停止，就回滚阻塞队列，如果没有则启动新线程。
           *
           * 3. If we cannot queue task, then we try to add a new
           * thread.  If it fails, we know we are shut down or saturated
           * and so reject the task.
           *
           * 3. 如果任务无法进入阻塞队列，则尝试创建新的进程。
           * 如果线程池被关闭或已经饱和，则执行拒绝方法。
           *
           */
       int c = ctl.get();
       if (workerCountOf(c) < corePoolSize) {	
           if (addWorker(command, true))
               return;
           c = ctl.get();
       }
       if (isRunning(c) && workQueue.offer(command)) {
           int recheck = ctl.get();
           if (! isRunning(recheck) && remove(command))
               reject(command);
           else if (workerCountOf(recheck) == 0)
               addWorker(null, false);
       }
       else if (!addWorker(command, false))
           reject(command);
   }
  ```

  

- public Future<?> submit(Runnable task)

  ```java
  public Future<?> submit(Runnable task) {
      if (task == null) throw new NullPointerException();
      RunnableFuture<Void> ftask = newTaskFor(task, null);
      execute(ftask);
      return ftask;
  }
  ```

- public <T> Future<T> submit(Runnable task, T result)

  ```java
  public <T> Future<T> submit(Runnable task, T result) {
      if (task == null) throw new NullPointerException();
      RunnableFuture<T> ftask = newTaskFor(task, result);
      execute(ftask);
      return ftask;
  }
  ```

- public <T> Future<T> submit(Callable<T> task)

  ```java
  public <T> Future<T> submit(Callable<T> task) {
      if (task == null) throw new NullPointerException();
      RunnableFuture<T> ftask = newTaskFor(task);
      execute(ftask);
      return ftask;
  }
  ```

可以看到submit实际上就是用传入的Callable或Runnable构造FutureTask然后丢给execute。



