---
title: Java单例模式
date: 2020-03-16 17:45:18
categories: 
- Java
tags:
- 设计模式
- 创建型模式
---

# Java单例模式

单例模式属于创建型模式，每个类只有一个实例，并为其他所有对象提供访问唯一该实例的的方式。

## 适用场景

只需要一个的对象，如硬件设备驱动对象（打印机or显卡驱动对象）、日志对象、创建需要消耗大量资源的对象（数据库连接）、代表耶稣的上帝对象等。

## 单例模式的实现

实现单例模式需要私有的构造函数，静态的实例对象与静态的访问方法。

### 饿汉式（线程安全）

在静态域中初始化单例的实例对象，所以单例的实例对象会在JVM加载该类时被创建，线程安全。

```java
public class Singleton {
	// 在静态域中初始化单例的实例对象 
	private static Singleton instance = new Singleton();
	// private 构造器
	private Singleton() {}
	// 访问该实例的方法
	public static Singleton getInstance() {
		return instance;
	}
}
```

### 懒汉式（非线程安全）

懒加载，在初次调用单例实例时初始化单例实例。

```java
public class Singleton {
	// 在静态域中声明单例的实例对象 
	private static Singleton instance;
	// private 构造器
	private Singleton() {}
	// 加锁的访问该实例的方法
	public static Singleton getInstance() {
		// 仅在初次访问时初始化单例实例
		if (instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
}
```

### 懒汉式（线程安全）

懒加载，在初次调用单例实例时初始化单例实例，synchronized加锁保证线程安全。

```java
public class Singleton {
	// 在静态域中声明单例的实例对象 
	private static Singleton instance;
	// private 构造器
	private Singleton() {}
	// 加锁的访问该实例的方法
	public static synchronized Singleton getInstance() {
		// 仅在初次访问时初始化单例实例
		if (instance == null) {
			instance = new Singleton();
		}
		return instance;
	}
}
```

### 懒汉式（双重校验锁DCL 线程安全）

懒加载，在调用单例实例时先检验单例实例是否创建，若未创建则初始化单例实例。
使用volatile关键词防止寄存器中读取的数据与内存中不一致。

```java
public class Singleton {
	// 在静态域中声明单例的实例对象 
	private volatile static Singleton instance;
	// private 构造器
	private Singleton() {}
	// 加锁的访问该实例的方法
	public static Singleton getInstance() {
		// 检验单例实例是否创建
		if (instance == null) {
			synchronized(Singleton.class) {
				// 进入同步代码块后再次检验
				if (instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```

### 懒汉式（IoDH延迟加载 线程安全）

利用内部编译后加载与主类无关的特性使用静态内部类延迟加载，在调用单例实例时显式加载静态内部类实现创建单例实例。

```java
public class Singleton {
	// 在静态内部类中声明单例的实例对象 
	private static class SingletonHolder {
		private static final Singleton INSTANCE = new Singleton();
	}
	// private 构造器
	private Singleton() {}
	// 加锁的访问该实例的方法
	public static Singleton getInstance() {
		return SingletonHolder.INSTANCE;
	}
}
```

### 枚举（线程安全）

通过枚举关键字enum定义Enum子类,利用枚举的特性保证线程安全与实例单一。

```java
public enum Singleton {
	INSTANCE
	public void whateverMethod();
}
```

## 参考文献

- Head First 设计模式