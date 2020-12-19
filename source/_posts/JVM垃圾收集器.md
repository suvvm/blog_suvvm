---
title: JVM垃圾收集器
date: 2020-04-27 21:55:15
categories: 
- Java
tags:
- JVM
- GC
mathjax: true
---

# 垃圾收集器

## Serial收集器

### 特点

- 最基本，发展最悠久的收集器
- JDK1.3.1前是新生代垃圾回收的唯一选择。
- Serial是单线程的（STW）进行垃圾回收时会暂停所有线程。
- 其依旧是客户端（-client）模式下的新生代默认收集器，进入STW状态可以高效回收，时间较短用户一般无法感知。
- 针对新生代采取复制算法
- 使用-XX:+UseSerialGC使用Serial收集器

### 回收过程

- 新生代回收时进入STW状态暂停所有线程
- 启动GC线程进行回收
- 恢复其他用户线程继续运行

### Serial Old收集器

Serial收集器的老年代版本，针对老年代采取标记整理算法

#### 回收过程

- 老年代回收时也是进入STW状态暂停所有线程
- 启动GC线程进行回收
- 之后恢复其他用户线程继续运行



## ParNew收集器

### 特点

- 多线程收集，相比Serial收集器来说可以拥有多个GC线程，可以看作是Serial收集器的多线程版本，收集算法、可控参数等于Serial收集器相等。
- 是服务器（-server）模式的首选收集器。
- 在单核CPU情况下性能并不如Serial收集器。
- 可以通过-XX:ParallelGCThreads=n配置垃圾回收线程数为n（建议于CPU数量一至）
- 使用-XX:+UseParNewGC可以将其指定为新生代收集器。
- 使用-XX:+UseConcMarkSweepGC指定使用CMS作为老年代收集器后会默认使用ParNew作为新生代收集器。

### 回收过程

- 新生代回收时进入STW状态暂停所有线程
- 启动指定数量的GC线程进行回收
- 之后恢复其他用户线程继续运行

## Parallel Scavenge收集器

### 特点

- Parallel Scavenge收集器是一个新生代收集器
- 是一个并行多线程垃圾收集器
- 采取复制算法
- 设计目的不是减少垃圾回收用户线程停止时间，而是提高吞吐量，保证总垃圾回收时长尽可能短
- 使用-XX:MaxGCPauseMillis设置最大垃圾收集时间，可以牺牲吞吐量减少单次收集时间
- 使用-XX:GCTimeRatio设置吞吐量大小，范围0~100，计算方式$\frac 1 {1 + n}$ 
- 使用XX:+UseAdaptiveSizePolicy开启自适应策略，开启后虚拟机会根据当前情况自动调整新生代各区域大小于晋升老年代阈值。

### 回收过程

- 触发GC后进入STW状态
- 运行MaxGCPauseMillis设置最大垃圾收集时间
- 到时间后不管GC程度如何直接开始运行用户线程直到再次触发GC

### Parallel Old收集器

Parallel Scavenge收集器的老年代版本，使用多线程标记整理算法，在老年代很大且硬件环境较为高级的情况下较为合适。

## CMS收集器

Concurrent Mark Sweep收集器是一种跨时代的收集器，边使用边回收。

### 特点

- 采取标记-清除算法，可能产生大量碎片
- CMS收集器对CPU敏感，可能降低用户线程执行效率
- 不等到老年代满后触发GC，而是为老年代流出部分空间供用户线程并发使用
- 无法处理浮动垃圾，即并发清理中产生的垃圾无法被当次回收清除，老年代预留空间不足时，会出现"Concurrent Mode Failure"，出现时会触发Full GC使用Serial Old收集器对老年代进行回收整理。
- 使用-XX:+UseCMSInitiatingOccupancyOnly可以指定GC触发时机否则将由虚拟机决定何时触发
  - CMSInitiatingOccupancyFraction 老年代阈值
  - 有永久代的话 CMSInitiatingPermOccupancyFraction 永久代阈值
- 使用+UseCMSCompactAtFullCollection在Full GC触发前进行碎片整理合并
- 使用-XX:CMSFullGCsBeforeCompaction可以控制在几次Full GC后进行碎片整理合并



### 回收过程

- 初始标记：只关联GC Root（所有Java线程当前栈引用、静态数据结构引用、常量池引用、运行时常量池引用）可以直接关联到的对象，速度很快，需要进入STW状态

- 并发标记：边标记边运行用户线程，遍历老年代可达对象，可能产生对象变动

  三色标记法

  - 白色，没有被搜索过的对象（垃圾对象）
  - 灰色，正在搜索的对象
  - 黑色，搜索完成的对象（存活对象）

- 重新标记：修正并发标记齐键因用户操作产生变动的对象标记记录，要标记的对象一般很少，需要进入STW状态

- 并发清除：在并发清除中用户线程可以与CMS收集器一台工作

## G1收集器

当前最先进的垃圾收集器，JDK9后的默认收集器

### 特点

- 简单可行的性能调优
- 使用-XX:+UseG1GC使用G1收集器
- 将内存划分为大小相等的大量区域，一般为数千个，这些区域又分为
  - Eden：用于存放新创建对象
  - Survivor：用于存放Eden区域提升的对象，满后将提升至Old区并进行回收
  - Old：老年代存放长久存活的对象
  - Humongous：Humongous是连续的区域，一个对象的大小超过一个区域的50%时，将被存放至Humongous区域
- 所有区域不是固定的，在清理完成后将成为待用区等待下一次分配。

### 回收过程

- Eden满后，使用复制算法将Eden与Survivor（From）区的存活复制到Survivor（To）区域（新），并清理Eden区与Survivor（From）区，空间不够直接提升至Old区，回收时只会扫描有引用Eden区与Survivor区对象的区域，而不是之前的RememberSet或全部老年代
- Survivor满后或目标对象到达存活阈值将提升至Old区
- Old占比超过规定后触发一次类似CMS的GC
  - 初始标记：触发年轻代GC标记可能有引用指向老年代的区域，需要进入STW状态
  - 扫描根区域：扫描初始标记中标记的区域，标记存活的老年代对象
  - 并发标记查询整个堆中存活的对象，同CMS并发标记
  - 重新标记：根据起始快照算法（SATB比CMS中的算法快很多）完成堆中存活对象的标记，需要进入STW状态
  - 清理：清理垃圾对象，并将清理后的空区域标记为空闲区域，需要进入STW状态
  - 拷贝：将存活对象拷贝至新的分配的Survivor（To）区域