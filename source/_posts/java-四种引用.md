---
title: java 四种引用
date: 2020-05-08 17:22:08
categories: 
- Java
tags:
- 引用
---

# Java 四种引用

## 强引用

强引用指的是与对象直接关联的引用，如直接创建一个对象并把这个对象赋值给目标类型引用

```java
Object[] objects = new Object[100];
```

被强引用所指向的对象在强引用消失前永远不会被垃圾回收器回收，但是可以通过显示将强引用赋值为null来消除强引用与目标对象的关系。

```java
objects = null;
```

## 软引用

软引用一般用于描述有用但非必要的引用（如图片缓存），在内存充足时软引用关联的对象不对被垃圾回收器回收，在其被回收之前一直可以通过get方法得到强引用，被回收后调用get方法将得到null。

JDK1.2后提供了SoftReference实现软引用

```java
SoftReference<Object> softReference = new SoftReference<>(new Object());
```

## 弱引用

弱引用一般用于描述用处不大的非必要的引用，当垃圾回收器开始垃圾回收时，无论内存空间是否充足，弱引用关联的对象都将被回收。

WeakHashMap中的key使用的就是虚引用。

```java
WeakReference<Object> weakReference = new WeakReference<>(new Object());
```

## 虚引用

虚引用无法通过get方法获得对象，创建虚引用时需要指明引用队列，在其指向的对象被回收后期会被加入引用队列表示其指向对象被销毁。一般用于探查对象何时从堆中被移除，比如可以利用它在上一张图片资源被从堆中移除后加载下一张图片。

```java
ReferenceQueue<Object> queue = new ReferenceQueue<>();
PhantomReference<Object> pr = new PhantomReference<>(new Object(), queue);
```



