---
title: JVM生命周期
date: 2020-03-29 18:29:00
categories:
- Java
tags:
- JVM
---

# JVM的生命周期

## JVM的启动
通过引导类加载器（bootstrap class loader Object就是由其加载的）创建初始类（引导类加载器会加载很多类包括initial class与Object类）调用初始类对应main方法来完成JVM的启动，这个类由虚拟机的具体实现指定。

## JVM的运行
Java虚拟机为了执行Java程序而启动，其任务便是执行Java程序。JVM伴随Java程序执行而启动，伴随Java程序终止而终止。所以执行Java程序时系统中在运行的是JVM进程。
可以用Java虚拟机进程状态工具jps（Java Virtual Machine Process Status Tool）命令来产看当前有权访问的HotSpot虚拟机进程。

## JVM的退出
JVM会在Java程序运行结束时退出（包括执行结束退出、异常终止退出、exit主动退出等）。
操作系统错误也可能导致JVM退出。

使用JNI Invocation API。