---
title: Java 集合
date: 2020-05-01 15:06:26
categories: 
- Java
tags:
- 集合
---

# Java 集合

Java集合主要包含两种类型，存储元素的Collection与存储键值对的Map。

## 接口与实体类关系

- Collection（接口）

  - List（接口）

    List接口代表的是一个用于存储数据的，有序的Collection，List可以控制每个元素插入的位置并通过索引对其进行访问，允许存储null值，允许存储相同元素

    - AbstractList（实体类）

      继承自AbstractCollection，实现了List接口的大部分方法

      - Vector（实体类）

        继承自AbstractList，Vector是线程安全的，其方法都由synchronized关键词标识，底层由数组实现，容量可变，有扩容（100%）和缩减方法，允许存放null值

      - ArrayList（实体类）

        继承自AbstractList，ArrayList是非线程安全的，大小可变的集合，只有扩容方法（50%），底层由数组实现，插入删除效率低，允许存放null值

      - AbstractSequentialList（实体类）

        继承自AbstractList，提供了对数据元素的链式访问

        - LinkedList（实体类）

          继承自AbstractSequentialList，LinkedList是非线程安全的链式集合，实现了Queue接口，底层由链表实现，查找效率低，允许null值

  - Set（接口）

    Set接口代表的是一个用于存储数据的，无序的Collection，允许存储null值

    - AbstractSet（实体类）

      继承自AbstractCollection实现了Set接口

      - HashSet（实体类）

        继承自AbstractSet，实现了Set接口，无重复元素，其中元素无序，底层使用hashMap实现，非线程安全，允许出现null值

        - LinkedHashSet（实体类）

          继承自HashSet，无重复元素，有可预测的迭代顺序（插入顺序），底层使用LinkedHasshMap，非线程安全，允许出现null值

      - TreeSet（实体类）

        继承自AbstractSet，提供有序的Set，实现了SortSet接口，元素无重复，非线程安全，不允许有null值

  - Queue（接口）

    Queue接口代表的是一个先进先出的数据存储结构，在Collection的基础上添加了特殊的插入、获取和检验操作，不允许存储null值

    - LinkedList实现了Queue接口

  - SortedSet（接口）

    SortedSet继承自Set代表了一个一个用于存储数据的，有序的Collection（按升序排列，用户无法控制位置），不允许存储null值

    - TreeSet实现了SortedSet接口

  - AbstractCollection（实体类）

    AbstractCollection是Collection借口的一个直接实现了，提供了Collection接口方法的最基本实现方式。

- Map（接口）

  Map接口代表的是一个键值对一一映射的存储结构 可以通过键来获取值。

  - SortedMap（接口）

    SortedMap接口代表的是有序的（key升序）的键值对存储结构

  - AbstractMap（实体类）

    实现了Map接口的部分方法

    - HashMap

      继承自AbstractMap，HashMap用于存储键值对，内容无序，最多允许一条key记录为null，之后的null记录会覆盖先前的null值，value可以为null，非线程安全

      - LinkedHashMap

        LinkedHashMap继承自HashMap，用于存储键值对，内容有序，除了维护hash表外还根据插入顺序维护了一个链表，同HashMap只允许一条key记录为null，非线程安全

    - Hashtable

      继承自Dictionary，实现了Map接口，内容无序，不允许出现null key与null value，线程安全

    - TreeMap

      继承自AbstractMap，基于红黑树实现了有序键值对存储，不允许null key允许null value，非线程安全

    - IdentityHashMap

      继承自AbstractMap，内容无序存储键值对，使用==而非equals比较key值，允许一条key记录为null，非线程安全

    - WeakHashMap

      继承自AbstractMap，非HashMap中的强引用，而是对key使用弱引用，堆空间不足时会将其回收，允许一条key记录为null，非线程安全，可利用其特性实现缓存。

      

  

