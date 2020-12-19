---

title: Java工厂模式
date: 2020-03-19 17:01:03
categories: 
- Java
tags:
- 设计模式
- 创建型模式
---

# Java工厂模式

工厂模式属于创建型模式，是创建对象的最佳方式，又良好的编程习惯简单工厂引出，分为工厂方法模式与抽象工厂模式

## 简单工厂模式（静态工厂模式）

将对应种类具体产品的创建交个唯一一个单独的工厂类负责。

### 适用场景

产品种类单一，工厂类负责创建的对象较少，不会造成工厂方法业务逻辑过于复杂；客户端只知道传入工厂的参数，不需要得知对象如何创建。如单独的披萨小店，店中可以制作的披萨种类有限。

### 优缺点

- 优点
  - 实现了对象创建与对象使用的分离
- 缺点
  - 工厂类不够灵活，增加具体产品需要修改工厂类的判断

### 实现

确定抽象产品类

```java
public abstract class Pizza extends Pizza {
    public abstract String showInfo();
}
```

实现具体产品

```java
public class CheesePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("芝士披萨");
    }
}
```

```java
public class PepperoniPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("腊肠披萨");
    }
}
```

```java
public class ClamPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("蛤蜊披萨");
    }
}
```

```java
public class VeggiePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("蔬菜披萨");
    }
    // 其他方法
}
```

创建简单工厂

```java
public class SimplePizzaFactory {	// 该工厂只用于创建披萨
	public static Pizza createPizza(String type) {	// 接收要创建披萨的种类创建对应种类的披萨实例
        Pizza pizza = null;
        if ("cheese".equals(type)) {	// 芝士披萨
            pizza = new CheesePizza();
        } else if ("pepperoni".equals(type)) {	// 腊肠披萨
            pizza = new PepperoniPizza();
        } else if ("clam".equals(type)) {	// 蛤蜊披萨
            pizza = new ClamPizza();
        } else if ("veggie".equals(type)) {	// 蔬菜披萨
            pizza = new VeggiePizza();
        }
        return pizza;
    }
}
```

创建披萨店（工厂的用户）

```java
public class PizzaStore {
    Public Pizza orderPizza(String type) {
        Pizza pizza;
        pizza = SimplePizzaFactory.createPizza(type);
    	return pizza;
    }
    // 其他方法
}
```

## 工厂方法模式

定义一个抽象核心工厂类与床单产品对象的接口，抽象工厂并不负责具体产品生产，而是将生产任务交给不同的派生工厂。

### 适用场景

客户端只知道产品特性，并不知道要构建的类。如披萨连锁店，不同店铺制作披萨的方式不同。

### 优缺点

- 优点
  - 核心工厂只需关注工厂类的接口定义，创建具体产品的实例交给派生子类去完成
- 缺点
  - 只能处理同种类型的产品

### 实现

确定抽象产品类

```java
public abstract class Pizza extends Pizza {
    public abstract String showInfo();
}
```

实现具体产品

```java
public class HotFatPigCheesePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("HotFatPig芝士披萨");
    }
}
```

```java
public class HotFatPigPepperoniPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("HotFatPig腊肠披萨");
    }
}
```

```java
public class HotFatPigClamPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("HotFatPig蛤蜊披萨");
    }
}
```

```java
public class HotFatPigVeggiePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("HotFatPig蔬菜披萨");
    }
    // 其他方法
}
```

```java
public class FatDickCheesePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("FatDick芝士披萨");
    }
}
```

```java
public class FatDickPepperoniPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("FatDick腊肠披萨");
    }
}
```

```java
public class FatDickClamPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("FatDick蛤蜊披萨");
    }
}
```

```java
public class FatDickVeggiePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("FatDick蔬菜披萨");
    }
    // 其他方法
}
```

创建抽象超类披萨店（抽象核心工厂）

```java
public abstract class PizzaStore {
    Public Pizza orderPizza(String type) {
        Pizza pizza;
        pizza = createPizza(type);
    	return pizza;
    }
    public abstract Pizza createPizza(String type);
}
```

创建实体子类披萨店（派生工厂）

```java
public class FatDickPizzaStore extend PizzaStore {
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        if ("cheese".equals(type)) {	// 芝士披萨
            pizza = new FatDickCheesePizza();
        } else if ("pepperoni".equals(type)) {	// 腊肠披萨
            pizza = new FatDickPepperoniPizza();
        } else if ("clam".equals(type)) {	// 蛤蜊披萨
            pizza = new FatDickClamPizza();
        } else if ("veggie".equals(type)) {	// 蔬菜披萨
            pizza = new FatDickVeggiePizza();
        }
        return pizza;
    }
}
```

```java
public class HotFatPigPizzaStore extend PizzaStore {
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        if ("cheese".equals(type)) {	// 芝士披萨
            pizza = new HotFatPigCheesePizza();
        } else if ("pepperoni".equals(type)) {	// 腊肠披萨
            pizza = new HotFatPigPepperoniPizza();
        } else if ("clam".equals(type)) {	// 蛤蜊披萨
            pizza = new HotFatPigClamPizza();
        } else if ("veggie".equals(type)) {	// 蔬菜披萨
            pizza = new HotFatPigVeggiePizza();
        }
        return pizza;
    }
}
```

## 抽象工厂模式

围绕一个抽象核心工厂创建其他工厂以实现各种不同种类的产品的构造，被生成的工厂能按照工厂方法模式生产产品。

### 适用场景

存在一个产品族中存在不只一个种类的产品可供客户端选择；如食品店同时在出售汉堡。

### 优缺点

- 优点
  - 可以保证客户端始终只使用同一个产品族中的产品
- 缺点
  - 产品族扩充困难

### 实现

确定抽象产品类

```java
public abstract class Pizza {
    public abstract String showInfo();
}
```

```java
public abstract class Hamburger {
    public abstract String showInfo();
}
```



实现具体产品

```java
public class CheesePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("芝士披萨");
    }
}
```

```java
public class PepperoniPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("腊肠披萨");
    }
}
```

```java
public class ClamPizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("蛤蜊披萨");
    }
}
```

```java
public class VeggiePizza extends Pizza {
    @Override
    public String showInfo() {
        System.out.println("蔬菜披萨");
    }
    // 其他方法
}
```

```java
public class BeefBurger extends Hamburger {
    @Override
    public String showInfo() {
        System.out.println("牛肉汉堡");
    }
    // 其他方法
}
```

```java
public class ChickenBurger extends Hamburger {
    @Override
    public String showInfo() {
        System.out.println("鸡肉汉堡");
    }
    // 其他方法
}
```

```java
public class ElderEightSecretBurger extends Hamburger {
    @Override
    public String showInfo() {
        System.out.println("老八秘制小汉堡");
    }
    // 其他方法
}
```



创建抽象核心超级工厂

```java
public abstract class Restaurant {
    public abstract Pizza createPizza(String type);
    public abstract Hamburger createHamburger(String type);
}
```

创建实体厨房

```java
public class PizzaStore extends Restaurant {	// 披萨部分
    @Override
    public Pizza createPizza(String type) {
        Pizza pizza = null;
        if ("cheese".equals(type)) {	// 芝士披萨
            pizza = new CheesePizza();
        } else if ("pepperoni".equals(type)) {	// 腊肠披萨
            pizza = new PepperoniPizza();
        } else if ("clam".equals(type)) {	// 蛤蜊披萨
            pizza = new ClamPizza();
        } else if ("veggie".equals(type)) {	// 蔬菜披萨
            pizza = new VeggiePizza();
        }
        return pizza;
    }
	@Override
     public Hamburger createHamburger(String type) {
         return null;
     }
    
}
```

```java
public class HamburgerStore extends Restaurant {	// 汉堡部分
    @Override
    public Pizza createPizza(String type) {
        return null;
    }
    @Override
    public Hamburger createHamburger(String type) {
        Hamburger hamburger = null;
        if ("Beef".equals(type)) {	// 牛肉汉堡
            pizza = new BeefBurger();
        } else if ("chicken".equals(type)) {	// 鸡肉汉堡
            pizza = new ChickenBurger();
        } else if ("eight".equals(type)) {	// 老八秘制小汉堡
            pizza = new ElderEightSecretBurger();
        }
        return hamburger;
    }
}
```

创建工厂生成器

```java
public class StoreProducter {
    public static Restaurant getStore(String choice) {
        Restaurant restaurant = null;
        if ("pizza".equals(choice)) {
            restaurant = new PizzaStore();
        } else if ("burger".equals(choice)) {
             restaurant = new HamburgerStore();
        }
        return restaurant;
    }
}
```

## 参考文献

- Head First 设计模式