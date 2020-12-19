---
title: Java类的加载过程
date: 2020-03-31 17:32:22
categories: 
- Java
tags:
- JVM
---

# 类的加载过程

## 类加载器

类加载器分为

- 引导类加载器（Bootstrap ClassLoader 由C与C++实现  嵌套在JVM内部）

- 自定义类加载器（User-Defined ClassLoader 由Java实现 系统类加载器继承自扩展类加载器属于自定义类加载器）

  - 扩展类加载器ExtentionClassLoader
  
    加载JRE_HOME中lib\ext下的jar包和字节码文件
  
  - 应用程序类加载器（系统类加载器）ApplicationClassLoader
  
    继承自扩展类加载器加载classpath的所有类
  
  

核心类库（JAVAHOME/jre/lib/rt.jar、resources.jar、sun.boot.class.path中包名为java、javax、sun）由BootstrapClassLoader加载，可以通过sum.misc.Launcher.getBootstrapClasspath().getURLs()获取BootstrapClassLader能加载的api路径。

## 加载Loading

加载.class既可以从本地加载，也可以在网络中获取

- 通过全类名获取此类二进制字节流
- 将此字节流代表的静态存储结构转化为方法区（仅限HotSpot虚拟机）中的运行时数据结构（JDK1.8前为永久代，JDK1.8后为元空间）
- 在内存中生成代表该类的Java.lang.Class对象作为该类的访问入口 
### 双亲委派模型

Java1.2后加入，一个类加载器在收到类加载请求后，会首先将这个请求委托给上级加载器，如果上级加载器还存在上级加载器就继续向上委托直到引导类加载器，如果上层加载器可以加载类就成功返回，否则下层类加载器才会尝试加载。

#### 优点

- 避免了类的重复加载
- 保证了安全，比如自己随便写了一个java.lang.Integer在加载时向上委托，上级引导类加载器发现了之加载过java.lang.Integer则直接返回加载过的Integer.class

## 链接

### 验证
验证加载的Class文件中的信息符合虚拟机要求(如Class文件开头的CA FE BA BE)
主要包括
- 文件格式验证

  验证字节流文件是否符合class文件的规范

- 元数据验证

  验证是否符合Java语法，是否有父类，类中的属性是否与父类冲突

- 字节码验证

  分析类的方法是否合法，符合规范

- 符号引用验证

  虚拟机将符号引用转化为直接引用时对类自身以外的信息进行验证

在可以确保没有问题的情况下，可以通过-Xverfity:none关闭大部分验证。

### 准备
为static变量分配内存并设置默认初始值，不会管实例变量（final  static变量被认为是常量在之前编译时就已经分配完毕，准会阶段会为常量显式指定值）。

### 解析
将该类常量池中对应所引用对象的符号引用转为直接引用。

## 初始化
执行线程安全的clinit方法（类构造器方法clinit不是类的构造器init），有编译器收集类变量的复制与静态代码块中的语句后合并得来，反编译后可以看到该方法。

