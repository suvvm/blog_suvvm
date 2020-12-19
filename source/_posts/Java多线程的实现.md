---
title: Java多线程的实现
date: 2020-03-15 19:39:32
categories: 
- Java
tags:
- 线程
---

# JAVA线程

## 线程与进程

众所周知，对于实现多了任务的操作系统，进程是资源的拥有者，也是处理机调度的基本单位。

线程继承了进程的一个基本属性，线程是处理机的调度单位。

所以线程是进程中的一个运行实体，是CPU的调度单位，有时将线程称为轻量级进程。

## Java 多线程的实现

- 通过继承Thread类创建线程
- 通过实现Runnable接口创建线程

### 通过继承Thread类创建线程

可以通过构造一个Thread类的子类，覆盖其run方法并调用start方法运行该线程，但此方法并不推荐，应该将要并行运行的任务与运行机制解耦合。

启动线程需要调用start方法而不是run方法，直接调用run方法会在当前线程执行run方法，start方法将创建一个执行run方法的新线程。

下方为通过继承Thread类创建线程实现两线程交替打印10次a与b

```java
package top.suvvm.thread;

/**
 * @ClassName: ExtendsTreadDemo
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/3/13 18:10
 */
public class ExtendsTreadDemo extends Thread{
    public static final Object flag = new Object();
    private String outPutVal;
    private Integer printCnt;
    public ExtendsTreadDemo(String outPutVal) {
        this.outPutVal = outPutVal;
        printCnt = 0;
    }
    @Override
    public void run() {
        while (true) {
            synchronized (flag) {
                flag.notify();
                try {
                    flag.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                printCnt++;
                if(printCnt == 11)
                    System.exit(0);
                System.out.println(this.outPutVal);
                flag.notify();
            }
        }
    }

    public static void main(String[] args) {
        ExtendsTreadDemo aThread = new ExtendsTreadDemo("a");   // 创建线程a 线程a当前处于新建态
        ExtendsTreadDemo bThread = new ExtendsTreadDemo("b");   // 创建线程b 线程b当前处于新建态
        aThread.start();    // 线程a进入runnable态
        bThread.start();    // 线程b进入runnable态
    }
}

```

### 通过实现Runnable接口创建线程

Runnable接口只提供了Run方法的虚函数声明。

```java
package top.suvvm.thread;

/**
 * @ClassName: RunnableDemo
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/3/13 15:36
 */
public class RunnableDemo implements Runnable{
    public static final Object flag = new Object();
    private String outPutVal;
    private Integer printCnt;
    public RunnableDemo(String outPutVal) {
        this.outPutVal = outPutVal;
        printCnt = 0;
    }
    @Override
    public void run() {
        while (true) {
            synchronized (flag) {
                flag.notify();
                try {
                    flag.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                printCnt++;
                if(printCnt == 11)
                    System.exit(0);
                System.out.println(this.outPutVal);
                flag.notify();
            }
        }
    }

    public static void main(String[] args) {
        Thread aThread = new Thread(new RunnableDemo("a"));   // 创建线程a 线程a当前处于新建态
        Thread bThread = new Thread(new RunnableDemo("b"));   // 创建线程b 线程b当前处于新建态
        aThread.start();    // 线程a进入runnable态
        bThread.start();    // 线程b进入runnable态
    }
}

```

### 使用线程并发库Condition

```java
package top.suvvm.thread;

import java.util.concurrent.*;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @ClassName: ConditionDemo
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/3/13 19:04
 */
public class ConditionDemo implements Callable<String> {
   //  public static final Object flag = new Object();
    public final ReentrantLock lock = new ReentrantLock(true);
    public static String outPutVal;
    private Integer printCnt;

    public ConditionDemo(String outPutVal) {
        this.outPutVal = outPutVal;
        printCnt = 0;
    }
    @Override
    public String call() throws Exception {
        while (true) {
            try {
                lock.lock();
                printCnt++;
                if (printCnt >= 11)
                    return "end" + " " + Thread.currentThread().getName();
                System.out.println(Thread.currentThread().getName() + " " + outPutVal);
                if ("a".equals(outPutVal))
                    outPutVal = "b";
                else
                    outPutVal = "a";
            } finally {
                lock.unlock();
            }
        }
    }
    public static void main(String[] args) throws Exception{

        ConditionDemo conditionDemo = new ConditionDemo("a");
        FutureTask aFutureTask = new FutureTask(conditionDemo);
        FutureTask bFutureTask = new FutureTask(conditionDemo);
        new Thread(aFutureTask).start();
        new Thread(bFutureTask).start();
        System.out.println(aFutureTask.get());
        System.out.println(bFutureTask.get());
    }
}

```

