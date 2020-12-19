---
title: Spring控制反转
date: 2020-03-28 17:33:36
categories: 
- 理论归纳
tags:
- Spring
---

# Spring 控制反转

控制反转（Inversion of Control）简写IoC，一种面向对象的设计原则，对象将获取其依赖对象的过程由主动获取转为被动。

控制指的是将创建对象交由IoC容器控制，简单来说就是IoC容器控制了对象。

反转指的是由IoC容器将被依赖的对象并注入到需要该依赖的对象中，实现两对象的解耦。

由IoC容器创建被依赖的对象并注入到需要该依赖的对象中，实现两对象的解耦。这种行为再2004年由[Martin Fowler](https://zh.m.wikipedia.org/wiki/Martin_Fowler) 明确描述为依赖注入（Dependenct Injection）这种行为更符合IoC的概念。

另一终IoC的方式是依赖查找，是指由被IoC容器控制的对象通过IoC容器提供的API主动查找其依赖的对象。

spring中的IoC容器有两种

- BeanFactory基本容器

  用于实例化、配置、管理bean，只提供最基础的注册bean、获取bean和一些简单的方法。

- ApplicationContext高级容器

  继承了

  ```
  EnvironmentCapable
  ListableBeanFactory
  HierarchicalBeanFactory
  MessageSource
  ApplicationEventPublisher
  ResourcePatternResolver
  ```

  提供了更多功能如提供解析文本消息的方式与消息国际化等



## IoC的优点

- 使控制对象间解耦，提升可维护性降低测试难度。
- IoC拥有工厂模式的所有优点
- IoC提升了可插拔性

## IoC缺点

- 使用反射创建被依赖对象，降低了对象创建的效率（现在反射创建对象效率已经大大改善）

