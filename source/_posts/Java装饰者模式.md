---
title: Java装饰者模式
date: 2020-03-18 17:22:16
categories: 
- Java
tags:
- 设计模式
- 结构型模式
---

# Java装饰者模式

装饰者模式是一种结构型模式，动态的将责任附加到对象上。若扩展功能，装饰者提供了比继承更有弹性的替代方案。创建一个装饰类，包装原有组件，在不改变原有方法的的前提下，提供额外功能。

## 适用场景

为了解决需要扩展大量子类的情况，如一种饮料可能加糖，加糖之后可能还会加水，加水之后肯还加盐之后可能还会加砒霜；这些糖、水盐、砒霜共同组成了一个物体；经典游戏植物大战僵尸“模仿者”，可以模仿很多植物，可以用于对应植物的功能。

## 优缺点

- 优点

  - 符合开闭原则

  - 装饰类与被装饰类相互独立
  - 是继承的有力补充

- 缺点

  - 装饰多层时比较复杂

## 实现

以饮料为例

创建抽象饮料类

```java
public abstract class Bevearge {
    // 描述
    String decsription = "Unknown Component";
    public String getDescription() {
        return decsription;
    }
    // 获取价格
    public abstract double cost();
}
```

饮料的装饰类调料类

```java
public abstract class CondimentDecorator extend Bevearge {
	public abstract String getDescription();
}
```

创建实体类浓缩咖啡

```java
public class Espresso extends Bevearge {
    public Espresso() {
        description = "Espresso";
    }
    public double cost() {
        return 1.99;
    }
}
```

创建实体类混合咖啡

```java
public class HouseBlend extends Bevearge {
    public Espresso() {
        description = "House Blend";
    }
    public double cost() {
        return .89;
    }
}
```

创建实体类深烘焙咖啡

```java
public class DarkRoast extends Bevearge {
    public Espresso() {
        description = "DarkRoast";
    }
    public double cost() {
        return .99;
    }
}
```



实现具体装饰者

摩卡咖啡

```java
public class Mocha extends CondimentDecorator {
    Beverage beverage;	// 记录被装饰者
    public Mocha(Beverage beverage) {
        this.beverage = beverage;
    }
    public String getDescription() {
        return beverage.getDescription + ", Mocha";
    }
    public double cost() {
        return .20 + beverage.cost();
    }
}
```

调料奶油

```java
public class Whip extend CondimentDecorator() {
    Beverage beverage;	// 记录被装饰者
    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }
    public String getDescription() {
        return beverage.getDescription() + "+Whip";
    };
    public double cost() {
        return .20 + beverage.cost();
    }
}
```

调料糖

```jav
public class Suger extend CondimentDecorator() {
    Beverage beverage;	// 记录被装饰者
    public Whip(Beverage beverage) {
        this.beverage = beverage;
    }
    public String getDescription() {
        return beverage.getDescription() + "+Suger";
    };
    public double cost() {
        return .20 + beverage.cost();
    }
}
```

测试代码

```java
public class Coffee{
    public static void main(String args[]) {
        Beverage espresso = new Espresso();	// 一杯不加调料的浓缩咖啡
        System.out.println(beverage.getDescription() + " $" + beverage.cost());
        
        Beverage darkRoast = new DarkRoast();	// 一杯深烘焙咖啡
        mocha = new Mocha(darkRoast);	// 用摩卡装饰它
        mocha = new Mocha(darkRoast);	// 用第二个摩卡装饰它
        mocha = new Whip(darkRoast);	// 用奶油装饰它
        System.out.println(darkRoast.getDescription() + " $" + darkRoast.cost());
        
        Beverage houseBlend = new HouseBlend();	// 一杯混合咖啡
        houseBlend = new Suger(houseBlend);	// 用糖装饰它
        houseBlend = new Whip(houseBlend);	// 用奶油装饰它
        System.out.println(houseBlend.getDescription() + " $" + houseBlend.cost());
    }
}
```

## 参考文献

- Head First 设计模式