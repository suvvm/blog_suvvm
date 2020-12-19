---
title: Java Object类方法
date: 2020-05-05 15:58:42
categories: 
- Java
tags:
- Object
---

# Java Object类方法

## registerNatives

```java
private static native void registerNatives();
```

本地方法，调用JNI函数用于注册本地方法。

## getClass

```java
public final native Class<?> getClass();
```

不可覆盖的本地方法，返回此Object的运行时类（java.lang.Class）

## hashCode

```java
public native int hashCode();
```

本地方法，返回其对象存储地址

## equals

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

equals用于比较两对象，Object中的equals直接对比在内存中是否为同一对象

## clone

```java
protected native Object clone() throws CloneNotSupportedException;
```

本地方法，对目标对象进行浅拷贝，返回拷贝得到的对象

## toString

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

返回类名与哈希值所组成的字符串

## notify

```java
public final native void notify();
```

本地方法，唤醒一个被当前对象wait()阻塞的线程并使其得锁

## notifyAll

```java
public final native void notifyAll();
```

本地方法，唤醒所有等待该对象的线程

## wait

```java
public final void wait() throws InterruptedException
```

尝试获取对象排它锁，失败则阻塞

```java
public final native void wait(long timeout) throws InterruptedException
```

尝试获取对象排它锁，失败则阻塞最长指定时间timeout

```java
public final void wait(long timeout, int nanos) throws InterruptedException
```

尝试获取对象排它锁，失败时间控制达到毫秒级

## finalize

```java
protected void finalize() throws Throwable { }
```

空方法实现，对象GC时会调用该方法，但是无法控制该方法被调用的时机，一般用于GC前判断目标对象状态并记录

