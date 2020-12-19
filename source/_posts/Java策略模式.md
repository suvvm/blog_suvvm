---
title: Java策略模式
date: 2020-03-17 17:34:42
categories: 
- Java
tags:
- 设计模式
- 行为模式
---

# Java 策略模式

策略模式属于行为模式，将类的行为封装为算法族，使其之间可以相互替换，让算法的变化独立于使用算法的对象。

## 适用场景

对象的行为和能在未来发生变化，如狗类可能有行为叫，但若继承狗类的玩具狗类可能不会叫，或狗类有行为跑，玩具狗类可能不会跑；学生类有行为上学，上学可能会乘公交车、骑自行车或步行等之类的需要动态选择算法的情况。

## 优缺点

- 优点
  - 可以自由的选择算法
  - 避免了冗长的if - else 判断
  - 可扩展性良好
- 缺点
  - 会出现大量的策略类
  - 所有的策略类都需要对外暴露

## 实现

创建策略接口

```java
public interface FlyStrategy {
    public void doFly();
}
```

创建接口实体类以实现具体行为

```java
public class FlyWithWings implements doBehavior {
    @Override
    public void doFly() {
        // 用翅膀飞行
    }
}
```

```java
public class FlyNoWay implements doBehavior {
    @Override
    public void doFly() {
        // 飞不起来
    }
}
```

```java
public class FlyRocketPowered implements doBehavior {
    @Override
    public void doFly() {
        // 火箭动力飞行
    }
}
```

创建要使用策略的超类

```java
public class Animal {
    // 声明策略
    private FlyStrategy flyStrategy;
    // 构造器
    public Animal(FlyStrategy flyStrategy) {
        this.flyStrategy = flyStrategy;
    }
    // 修改策略的方法
    public setFlyStrategy(FlyStrategy flyStrategy) {
        this.flyStrategy = flyStrategy;
    }
    // 执行策略的方法
    public void executeFlyStrategy() {
        flyStrategy.doFly();
    }
}
```

使用具体策略

```java
Animal duck = new Animal(new FlyWithWings());
Animal forg = new Animal(new FlyNoWay());
Animal human = new Animal(new FlyRocketPowered());
duck.executeFlyStrategy();
forg.executeFlyStrategy();
human.executeFlyStrategy();
```

## 参考文献

- Head Firsh设计模式