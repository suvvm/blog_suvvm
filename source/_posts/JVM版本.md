---
title: JVM版本
date: 2020-03-29 18:32:41
categories:
- Java
tags:
- JVM
---

# JVM版本

## SUN Classic VM（1996）

SUN Classic VM是世界上第一款商用Java虚拟机，于JDK1.0发布，于JDK1.4（2002）时被淘汰。

其不像现主流编译器一样提供解释器于即时编译器JIT（just-in-time compiler），SUN Classic VM只提供解释器，无法寻找热点代码缓存指令，即使外挂JIT也无法与现有解释器协同工作，所以性能差劲。

现主流HotSpot虚拟机中内置了Classic VM。

## Exact VM（1998）

由SUN在JDK1.2时发布，解决了Classic VM没有即时编译器的问题，采取了即时编译器与解释器混合工作的模式，也有热点代码的探测功能。

提供准确式内存管理，虚拟机可以知道内存中某个位置的数据具体类型。

但是只在SUN公司Solaris平台发布。

## HotSpot VM（2000）

随JDK1.3发布，HotSpot成为默认虚拟机直到现在依旧是使用最广泛的虚拟机，拥有方法区。从服务器、桌面应用、移动端、嵌入式都要HotSpot的应用，占据着绝对的市场地位，其命名体现了热点代码的探测功能（通过计数器找到热点代码缓存到本地），编译器（优化响应时间）与即时解释器（优化执行性能）混合。

##  JRockit（2002）

由Appeal Virtual Machines开发于2002年被BEA收购又于2008年被Oracle收购。API兼容性很好，除JVM部份外JRockit的JER .class文件与HotSpot 的JRE完全一致。

JRockit主要针对服务器端，专注于响应时间不注重应用启动速度，不包含解析器所有代码都靠JIT编译执行。

JRockit用有面向延时敏感的JRockit Real Time和用于监控管理生产环境的MissionControl服务套件。

号称世界上最快的JVM。

JDK8中Oracle基本整合了HotSpot与JRockit。

## J9

J9是2017年由IBM发布的商用虚拟机。

是与HotSpot类似的针对服务器端、桌面应用、嵌入式的多用途虚拟机。

是许多IBM Enterprise中间件产品所使用的虚拟机，有较高的性能、良好的可伸缩性与可靠性。

也号称世界上最快的JVM。

2017年IBM发布了开源J9 Eclipse OpenJ9，同年项目被改名为Eclipse Foundation第一版v0.8.0于2018年发布。

## KVM CDC/CLDC HotSpot

Oracle针对Java ME的虚拟机，较为轻量，主要用于传感器、老人机、曾经的诺基亚。

## Azul VM

由Azul System用HotSpot改造的高性能虚拟机，需要依赖Azul专有硬件系统Veaga，可以管理数十个cpu数百G内存。

## Zing JVM

由Azul System在2010年转向软件后发布的通用JVM，在低延迟于快速预热方面优于HotSpot。

## Liquid VM

由BEA由JRockit基础开发的运行在BEA Hypervisor系统上的虚拟机，可以越过操作系统直接控制硬件，但项目随着JRockit终止而终止。

## Apache Harmony

扯皮产物 于JDK1.5 JDK1.6兼容由IBM与Intel联合开发没有获得JCP认证，现已经退出历史舞台。

## Microsoft JVM

微软为IE3开发的JVM，只能在windows中使用，有意被告的原有windowsXP SP3中被废除转而使用HotSpot

## Taobao JVM

由AliJVM团队发布，阿里基于OpenJDK开发了AlibabaJDK，Taobao JVM是针对HotSpot进行深度定制的开源高性能虚拟机，已在淘宝、天猫上线。

- 创建CGIH（GC invisible heap）

  实现了off-heap，生命周期较长的对象由heap中移到了heap之外，且GC不能管理GCIH内部的对象，降低了GC的会搜频率，提升了GC的回收效率

- GCIH中的对象可在多个JVM进程中共享

- 使用crc32指令实现JVM intrinsic 降低JNI调用开销

- 针对大数据的ZenGC

但taobao vm硬件严重依赖intel的cpu损失了兼容性，但显著提高了性能。

## Graal VM

2018年由Oracle发布，在HotSpot基础上增强的全栈虚拟机，可作为Java、C、C++、JavaScript、Python、R等多种语言的运行平台，支持不同语言的接口混用，支持各种语言的本地库。

原理是将这些语言代码或编译中间文件通过解释器转换为能被Graal VM接受的中间表示。