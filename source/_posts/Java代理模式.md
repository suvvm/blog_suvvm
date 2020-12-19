---
title: Java代理模式
date: 2020-04-19 16:23:21
categories: 
- Java
tags:
- 设计模式
- 结构模式
---

# Java代理模式

代理模式是一种结构模式，使用一个类控制另一个类的访问。总结来说就是创建一个持有被代理对象的代理类，通过代理类提供的接口适用被代理类实现目标功能。

## 适用场景

想要针对某个类的对象做一些访问控制或者无法直接访问目标对象。如你是一个烂好人别人让你干啥你都干，你太累了，找个带哥，所有找你办事的人都要经过你带哥，并切只能找你办带哥规定的事情。你是个👴，有一堆在外地的孙子，你直接打不到你的孙子，你找到了一个名为滴滴打人的代理，你通过联系本地的滴滴打人代理人通知到了和你孙子同城的滴滴打人代理人，把你孙子揍了一遍。

## 优缺点

- 优点
  - 通过创建代理对象提高了扩展性
  - 代理对象起到了保护目标对象的作用

- 缺点
  - 要为被代理类提供代理类，导致类的总数增加增加了系统的复杂度
  - 效率不如直接适用被代理对象高

## 实现

创建被代理类

```java
public class You {
    public void doThis() {...}
    public void doThat() {...}
}
```

创建代理类

```java
public class BigBrother {
    private You you;
    public BigBrother(You you) {
        this.you = you;
    }
    public void todo() {
        you.doThis();
        you.doThat();
        you.doThis();
        you.doThat();
        you.doThis();
        you.doThat();
    }
}
```

