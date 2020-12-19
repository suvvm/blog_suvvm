---
title: Java 生产者消费者模式
date: 2020-05-09 20:54:59
categories: 
- Java
tags:
- 线程
- 设计模式
---

# Java 生产者消费者模式

生产者消费者模式将产生数据的执行流叫做生产者，将处理数据的执行流叫做消费者，为生产者于消费者提供数据缓冲区，生产者将数据写入缓冲区，消费者从缓冲区中获取数据，实现生产者于消费者的同步。

## 优点

- 优点

  - 降低耦合度

    由于有缓冲区的概念，生产者与消费者之间没有直接的依赖关系，可以降低系统耦合度

  - 提高效率

    生产者与消费者并发允许，操作数据缓冲区，提高了系统效率

  - 平衡生产者与消费者之间的处理速度差

## 实现

### 排它锁

```java
import java.util.LinkedList;
import java.util.Queue;

/**
 * @ClassName: BoundedBuffer
 * @Description: 生产者消费者的有界缓冲区
 * @Author: SUVVM
 * @Date: 2020/5/9 19:28
 */
public class BoundedBuffer {
    private Object flag;
    private Integer maxSize;
    private Queue buffer;

    public BoundedBuffer(Object flag, Integer maxSize, Queue buffer) {
        this.flag = flag;
        this.maxSize = maxSize;
        this.buffer = buffer;
    }
    public Object getFlag() {
        return flag;
    }

    public Integer getMaxSize() {
        return maxSize;
    }

    public Queue getBuffer() {
        return buffer;
    }

    public boolean isFull() {
        return maxSize == buffer.size();
    }

    public boolean isEmpty() {
        return buffer.isEmpty();
    }
    public static void main(String[] args) {
        BoundedBuffer boundedBuffer = new BoundedBuffer(new Object(), 10, new LinkedList());
        Thread p1 = new Thread(new Producer(boundedBuffer), "Producer001");
        Thread p2 = new Thread(new Producer(boundedBuffer), "Producer002");
        Thread c1 = new Thread(new Consumer(boundedBuffer), "Consumer001");
        Thread c2 = new Thread(new Consumer(boundedBuffer), "Consumer002");
        p1.start();
        p2.start();
        c1.start();
        c2.start();
    }
}

/**
 * @ClassName: Producer
 * @Description: 生产者，每秒向缓冲区中添加一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:36
 */
class Producer implements Runnable {
    private BoundedBuffer buffer;

    public Producer(BoundedBuffer buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (buffer.getFlag()) {
                while (buffer.isFull()) {
                    try {
                        System.out.println("Producter:buffer is full");
                        buffer.getFlag().wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                Object val = new Object();
                buffer.getBuffer().add(val);
                System.out.println("Productor " + Thread.currentThread().getName() + " put " + val);
                buffer.getFlag().notifyAll();
            }

        }
    }
}

/**
 * @ClassName: Consumer
 * @Description: 消费者，每2秒从缓冲区中取出一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:53
 */
class Consumer implements Runnable {
    private BoundedBuffer buffer;

    public Consumer(BoundedBuffer buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (buffer.getFlag()) {
                while (buffer.isEmpty()) {
                    try {
                        buffer.getBuffer().wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                Object val = buffer.getBuffer().poll();
                System.out.println("Consumer " + Thread.currentThread().getName() + " poll " + val);
                buffer.getFlag().notifyAll();
            }
        }
    }
}
```

### 可重入锁

```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * @ClassName: BoundedBuffer02
 * @Description: 生产者消费者的有界缓冲区
 * @Author: SUVVM
 * @Date: 2020/5/9 19:28
 */
public class BoundedBuffer02 {
    private Lock lock;
    private Integer maxSize;
    private Queue buffer;
    private Condition emptyCondition;
    private Condition fullCondition;

    public BoundedBuffer02(Lock lock, Integer maxSize, Queue buffer) {
        this.lock = lock;
        this.maxSize = maxSize;
        this.buffer = buffer;
        emptyCondition = lock.newCondition();
        fullCondition = lock.newCondition();
    }
    public Lock getLock() {
        return lock;
    }

    public Integer getMaxSize() {
        return maxSize;
    }

    public Queue getBuffer() {
        return buffer;
    }

    public boolean isFull() {
        return maxSize == buffer.size();
    }

    public boolean isEmpty() {
        return buffer.isEmpty();
    }

    public Condition getEmptyCondition() {
        return emptyCondition;
    }

    public Condition getFullCondition() {
        return fullCondition;
    }

    public static void main(String[] args) {
        BoundedBuffer02 boundedBuffer = new BoundedBuffer02(new ReentrantLock(), 10, new LinkedList());
        Thread p1 = new Thread(new Producer02(boundedBuffer), "Producer001");
        Thread p2 = new Thread(new Producer02(boundedBuffer), "Producer002");
        Thread c1 = new Thread(new Consumer02(boundedBuffer), "Consumer001");
        Thread c2 = new Thread(new Consumer02(boundedBuffer), "Consumer002");
        p1.start();
        p2.start();
        c1.start();
        c2.start();
    }
}

/**
 * @ClassName: Producer02
 * @Description: 生产者，每秒向缓冲区中添加一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:36
 */
class Producer02 implements Runnable {
    private BoundedBuffer02 buffer;

    public Producer02(BoundedBuffer02 buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            buffer.getLock().lock();
            try {
                while (buffer.isFull()) {
                    try {
                        System.out.println(Thread.currentThread().getName() + ":buffer is full");
                        buffer.getFullCondition().await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                Object val = new Object();
                buffer.getBuffer().add(val);
                System.out.println("Productor " + Thread.currentThread().getName() + " put " + val);
                buffer.getEmptyCondition().signal();
            } finally {
                buffer.getLock().unlock();
            }
        }
    }
}

/**
 * @ClassName: Consumer02
 * @Description: 消费者，每2秒从缓冲区中取出一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:53
 */
class Consumer02 implements Runnable {
    private BoundedBuffer02 buffer;

    public Consumer02(BoundedBuffer02 buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            buffer.getLock().lock();
            try {
                while (buffer.isEmpty()) {
                    try {
                        System.out.println(Thread.currentThread().getName() + ":buffer is empty");
                        buffer.getEmptyCondition().await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                Object val = buffer.getBuffer().poll();
                System.out.println("Consumer " + Thread.currentThread().getName() + " poll " + val);
                buffer.getFullCondition().signal();
            } finally {
                buffer.getLock().unlock();
            }
        }
    }
}
```

### 阻塞队列

```java
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

/**
 * @ClassName: BoundedBuffer03
 * @Description: 生产者消费者的有界缓冲区
 * @Author: SUVVM
 * @Date: 2020/5/9 19:28
 */
public class BoundedBuffer03 {
    public static void main(String[] args) {
        BlockingQueue<Object> boundedBuffer = new ArrayBlockingQueue<>(10);
        Thread p1 = new Thread(new Producer03(boundedBuffer), "Producer001");
        Thread p2 = new Thread(new Producer03(boundedBuffer), "Producer002");
        Thread c1 = new Thread(new Consumer03(boundedBuffer), "Consumer001");
        Thread c2 = new Thread(new Consumer03(boundedBuffer), "Consumer002");
        p1.start();
        p2.start();
        c1.start();
        c2.start();
    }
}
/**
 * @ClassName: Producer03
 * @Description: 生产者，每秒向缓冲区中添加一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:36
 */
class Producer03 implements Runnable {
    private BlockingQueue buffer;

    public Producer03(BlockingQueue buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(1000);
                Object val = new Object();
                buffer.put(val);
                System.out.println("Productor " + Thread.currentThread().getName() + " put " + val);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

/**
 * @ClassName: Consumer03
 * @Description: 消费者，每2秒从缓冲区中取出一条数据
 * @Author: SUVVM
 * @Date: 2020/5/9 19:53
 */
class Consumer03 implements Runnable {
    private BlockingQueue buffer;

    public Consumer03(BlockingQueue buffer) {
        this.buffer = buffer;
    }

    @Override
    public void run() {
        while (true) {
            try {
                Thread.sleep(2000);
                Object val = buffer.take();
                System.out.println("Consumer " + Thread.currentThread().getName() + " poll " + val);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```