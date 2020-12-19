---
title: Java适配器模式
date: 2020-03-23 17:02:32
categories: 
- Java
tags:
- 设计模式
- 结构型模式
---

# Java适配器模式

适配器模式属于结构型模式，适配器模式将一个类的接口转换为客户期望的另一个接口，让原本不兼容的类可以合作无间。

## 适用场景

客户想要使用现有的类，但此类的接口并不符合要求。如鸭子不够数想拿鸡来凑，220V电压使用110V电器，某项目需要女孩子但是女孩子不够就只能上女装大佬。

## 优缺点

- 优点
  - 可以让任何两个没有关联的类一起运行
  - 提高了类的复用性
- 缺点
  - 使用过多适配器将大幅降低程序可读性

## 实现

由于Java单继承，无法实现类适配器，只能实现对象适配器

创建两个不匹配的接口

```java
public interface Woman {
    public void makeup();
    public void wearSkirt();
}
```

```java
public interface Man {
    public void fight();
    public void wearTrousers();
}
```

创建实现对应接口的实体类

```java
public class Girl implements Woman {
    public void makeup() {
        System.out.Println("Make up!");
    }
    public void wearSkirt() {
         System.out.Println("I'm wear a skirt!");
    }
}
```

```java
public class boy implements Woman {
    public void fight() {
        System.out.Println("die die die!");
    }
    public void wearTrousers() {
         System.out.Println("I'm wear a trousers");
    }
}
```

创建适配器

```java
public class Crossdresser implements Woman {	// 实现想转换为的类型
    Boy boy;
    public Crossdresser(Boy boy) {	// 获取对象引用
        this.boy = boy;
    }
    public void makeup() {
        System.out.Println("Make up!");
    }
    public void wearSkirt() {
         System.out.Println("I'm wear a skirt too!");
    }
}
```

测试适配器

```java
@Test
public void AdapterTest() {
    Boy boy = new Boy();	// 创建一个男孩
    Girl girl = new Girl();	// 创建一个女孩
    Woman crossdresser = new Crossdresser(boy);	// 将男孩包装进一个女装大佬适配器里
    System.out.println("The boy say :");	// 测试男孩
    boy.fight();
    boy.wearTrousers();
    
    System.out.println("The girl say :");	// 测试男孩
    girl.makeup();
    gitl.wearSkirt();
    
    System.out.println("The crossdresser say :");	// 测试女装大佬
    crossdresser.makeup();
    crossdresser.wearSkirt();
    
}
```

## 参考文献

- Head First 设计模式