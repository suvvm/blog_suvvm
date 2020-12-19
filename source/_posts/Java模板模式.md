---
title: Java模板模式
date: 2020-03-25 15:20:36
categories: 
- Java
tags:
- 设计模式
- 行为模式
---

# Java模板模式

模板模式又叫做模板方法模式，是一种行为模式，其思想是在一个方法中定义一个算法框架，而将一些步骤延迟到子类中，使得子类在不改变原有算法结构的情况下实现重新定义算法中的某些步骤。

## 适用场景

针对某个类的子类有一些方法有共同的基础逻辑，但是需要不同的具体实现。如咖啡和茶都需要烧水、冲泡、倒杯与加调料，但是冲泡的方式与所加的调料可能都不相同。

## 优缺点

- 优点
  - 可以确保算法结构不变的情况下，扩展可变内容
  - 将公共代码提取，易于维护
- 缺点
  - 采取继承方式，每一种针对当前算法的不同实现都需要一个子类，可能导致类的数量过于臃肿

## 实现

创建抽象超类与模板方法

```java
public abstract class Beverage {
    public final void prepareRecipe() {	// 模板方法
        boilWater();	// 烧水
        brew();	// 冲泡
        pourInCup();	// 倒杯
        addCondiments();	// 加调料
    }
    // 需要由子类提供的方法
    abstract void brew();
    abstract void addCondiments();
    // 不需要由子类提供的方法
    void boilWater() {
        System.out.println("烧水");
    }
    void pourInCup() {
        System.out.println("倒入杯中");
    }
}
```

创建子类

```java
public class Tea extends Beverage {	// 茶
    public void brew() {
        System.out.println("泡茶");
    }
    public void addCondiments() {
        System.out.println("加柠檬");
    }
}
```

```java
public class Coffee extends Beverage {	// 茶
    public void brew() {
        System.out.println("冲咖啡");
    }
    public void addCondiments() {
        System.out.println("加糖加牛奶");
    }
}
```

## 参考文献

- Head First 设计模式