---
title: Java HashMap
date: 2020-03-30 21:14:05
categories: 
- Java
tags:
- HashMap
- 集合
---

# Java HashMap详解

## 散列值计算

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

```java
/**
*方法putVal中
*/
tab[i = (n - 1) & hash]	// n 为tab当前长度
```

由于要进行(n - 1) & hash运算，在数组长度较小时散列码的高16位并没有被利用，所以将散列码高16位扩展至低16位，并与当前长度减一做&运算得出最终的数组下标。

| 长度 | 影响散列值的位 |
| ---- | -------------- |
| 1    | 低1位          |
| 2    | 低1位          |
| 3    | 低2位          |
| 4    | 低2位          |
| 5    | 低3位          |
| 6    | 低3位          |
| 7    | 低3位          |
| 8    | 低3位          |
| 9    | 低4位          |

以此类推

## 静态域属性

- DEFAULT_INITIAL_CAPACITY

  ```java
  static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;	// 16
  ```

  HashMap默认初始容量16，2的幂数

- MAXIMUM_CAPACITY

  ```Java
  static final int MAXIMUM_CAPACITY = 1 << 30;
  ```

  HarshMap最大容量1,073,741,824‬，2的幂数，由带参构造器提供，如果指定的容量高于最高容量将使用最高容量

- DEFAULT_LOAD_FACTOR

  ```java
  static final float DEFAULT_LOAD_FACTOR = 0.75f;
  ```

  HashMap默认负载因子0.75 构造HashMap时没有指定负载因子时默认负载因子为0.75

- TREEIFY_THRESHOLD

  ```java
  static final int TREEIFY_THRESHOLD = 8;
  ```

  HashMap树化阈值，再存储数据时若链表长度大于该值则将链表转为红黑树

- UNTREEIFY_THRESHOLD

  ```
  static final int UNTREEIFY_THRESHOLD = 6;
  ```

  HashMap树还原阈值，在进行扩容运算（resize）时会重新计算数据存储位置，若扩容后的红黑树的结点数小于此值时将红黑树还原为链表

- MIN_TREEIFY_CAPACITY

  ```
  static final int MIN_TREEIFY_CAPACITY = 64;
  ```

  HashMap最小树化容量，只有当HashMap容量大于该值时才允许将链表转化为红黑树

## 构造器

- 无参构造

  ```java
  public HashMap() {
      this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
  }
  ```

  以默认容量16，默认负载因子为0.75构造HashMap

- 指定容量构造

  ```java
  public HashMap(int initialCapacity) {
      this(initialCapacity, DEFAULT_LOAD_FACTOR);
  }
  ```

  以指定的容量，默认负载因子为0.75构造HashMap，如果初始容量为负，则抛出IllegalArgumentException

- 指定容量，指定负载因子构造

  ```java
  public HashMap(int initialCapacity, float loadFactor) {
      if (initialCapacity < 0)
          throw new IllegalArgumentException("Illegal initial capacity: " +
                                             initialCapacity);	// 指定容量为负数抛出异常
      if (initialCapacity > MAXIMUM_CAPACITY)	
          initialCapacity = MAXIMUM_CAPACITY;	// 指定容量大于最大容量使用最大容量
      if (loadFactor <= 0 || Float.isNaN(loadFactor))
          throw new IllegalArgumentException("Illegal load factor: " +
                                             loadFactor);	// 指定负载因子小于等于0或负载因子非有效数值抛出异常
      this.loadFactor = loadFactor;	// 设置负载因子
      this.threshold = tableSizeFor(initialCapacity);	// 设置容量
  }
  ```

## 插入

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // 如果当前table为null 或者当前table的长度为0 执行首次扩容
    if ((tab = table) == null || (n = tab.length) == 0)	
        n = (tab = resize()).length;	// n 记录为扩容后table的长度，用于之后根据hash值判断存储位置
    // 目标hash位置没有冲突
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {	// 目标位置存在hash冲突
        Node<K,V> e; K k;
       	// 当前key与目标位置key相等（key值从图）
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)	// 目标位置为红黑树
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);	// 执行红黑树插入
        else {	// hashCode值相等的两个key
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {	// 找到链表尾
                    p.next = newNode(hash, key, value, null);	// 新的键值对插入链表尾
                    if (binCount >= TREEIFY_THRESHOLD - 1) // 链表长度大于树化阈值
                        treeifyBin(tab, hash);	// 树化
                    break;
                }
                // 继续判断链表下一个元素是否与目标key相等 不相等则继续向后遍历
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // 存在于目标key相等的key
            V oldValue = e.value;	// 记录old值value
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;	// 更新目标值
            afterNodeAccess(e); // 为LinkedHashMap留的后路 将更新后的结点移动到链表尾
            return oldValue;	// 返回被替换的value
        }
    }
    ++modCount;	// 修改次数加一 用于ConcurrentModificationException 乐观锁 判断版本号
    if (++size > threshold)	// 当前hashmap大小大于扩容阈值进行扩容
        resize();
    afterNodeInsertion(evict);	// 为LinkedHashMap留的后路 除掉LRU结点 
    return null;
}
```



## 扩容

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;	// 获取先前table的长度
    int oldThr = threshold;	// 保留上次扩容值的大小
    int newCap, newThr = 0;
    if (oldCap > 0) {	// 不为初次扩容
        if (oldCap >= MAXIMUM_CAPACITY) {	// 先前table容量已经大于HashMap最大容量
            threshold = Integer.MAX_VALUE;	// 下次扩容的阈值 int最大值
            return oldTab;	// 不进行扩容返回
        }
        // table新容量等于先前table容量的一倍最多至HashMap最大容量
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)	
            newThr = oldThr << 1; // 如果先前table容量大于默认初始容量，新的扩容阈值翻倍
    }
    else if (oldThr > 0) // 初次扩容，用户有指定初始容量，将用户指定容量设为新的table容量
        newCap = oldThr;
    else {               // 初次扩容 默认
        newCap = DEFAULT_INITIAL_CAPACITY;	// 新table大小指定为HashMap默认容量
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);	// 新下次扩容阈值记为 默认阈值 * HashMap默认容量
    }
    if (newThr == 0) {	// 新扩容阈值为0
        float ft = (float)newCap * loadFactor;	// 新容量乘扩容因子
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);	// 指定下次扩容阈值 为刚刚计算的的结果或int最大值
    }
    threshold = newThr;	// 记录新的扩容阈值
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];	// 根据新大小创建一个新的Node数组作为table
    table = newTab;
    if (oldTab != null) {	// 旧table存在
        for (int j = 0; j < oldCap; ++j) {	// 遍历旧table
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {	// 旧table 对应位置存在元素则用e保存
                oldTab[j] = null;
                if (e.next == null)	// e元素所致位置没有hash冲突
                    newTab[e.hash & (newCap - 1)] = e;	// 直接根据e的hash值将e存放至新table目标位置
                else if (e instanceof TreeNode)	// e为红黑树结点
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);	// 将红黑树复制到新表中，将红黑树分为高端低端两部分，并判断是否需要还原，使其更好的分散在新容器中
                else { // e为链表结点 将链表分为高低两端 低端为扩容后不需要移动部分 高端为扩容后需要移动的部分
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 由于table的实际容量扩大为旧容量的两倍，之前putVal方法记录元素位置的方法要与上当时table的长度的大小
                        // 也就是是说当前table扩容一倍后新的hash码多了一位
                        // 只需要根据多的这一位是1或0来判断链表中的元素是否需要变动位置即可
                        if ((e.hash & oldCap) == 0) {	// 与原table容量进行与运算，如果为0则新增位为0
                            // 将其加入低端链表中
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {	// 新增位为1，将其加入高端链表中
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {	// 低端链表存在
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {	// 高端链表存在
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```



## ConcurrentHashMap

ConcurrentHashMap通过CAS算法加synchronized关键词来实现了非阻塞式的设计，更新时锁定部分数据而不会锁定全表

读取操作是完全非阻塞的

put操作不允许加入null key与null value，之后计算hash值，在记录Node的table里找到对应位置，如果对应位置没有元素，就通过CAS的方式添加元素至该位置，如果目标位置有元素即发生hash冲突时就用synchronized的方式加锁后进行插入操作。若插入时有扩容正在发生，则尝试协助迁移

扩容时允许多个线程协作迁移数据，最后收尾线程负责校验数据。



### 插入

```java
final V putVal(K key, V value, boolean onlyIfAbsent) {
    // 不允许插入null key 或 null value
    if (key == null || value == null) throw new NullPointerException();
    int hash = spread(key.hashCode());	// 限制可用位数的hash值
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {	// 尝试插入目标键值对到table中直至成功
        Node<K,V> f; int n, i, fh;
        // 如果当前table为null 或者当前table的长度为0 执行初始化table
        if (tab == null || (n = tab.length) == 0)
            tab = initTable();
        // 标hash位置没有冲突 cas 将值插入目标位置 成功则跳出循环
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // hash值为-1代表发现ForwardingNode节点，说明此时table正在扩容，本线程将尝试协助数据迁移
        else if ((fh = f.hash) == MOVED)
            tab = helpTransfer(tab, f);	// 尝试协助
        else {	// 目标位置出现hash冲突
            V oldVal = null;
            synchronized (f) {	// 对table目标位置加锁
                if (tabAt(tab, i) == f) {	// 再次校验hash冲突是否存在
                    if (fh >= 0) {	// table目标位置是链表
                        binCount = 1;
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 若有相等key值则更新目标value
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            Node<K,V> pred = e;
                            // 没有相等key值将目标键值对插入链表尾
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) {	// table目标位置是红黑树
                        Node<K,V> p;
                        binCount = 2;
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                              value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            if (binCount != 0) {
                if (binCount >= TREEIFY_THRESHOLD)	// 判断是否需要树化
                    treeifyBin(tab, i);
                if (oldVal != null)	// 若替换旧值没有插入结点返回旧值
                    return oldVal;
                break;
            }
        }
    }
    addCount(1L, binCount);	// 插入计数加一
    return null;
}
```



### 初始化

```java
private final Node<K,V>[] initTable() {
    Node<K,V>[] tab; int sc;
    // 二次校验 table为null 或长度为0时执行初始化操作
    while ((tab = table) == null || tab.length == 0) {
        // sizeCtl 为表初始化和调整大小的控件，如果为负，则正在初始化或调整表的大小
        // -1代表正则初始化， 否则等于-（1+活动的调整大小线程数）
        // 当table为空时，将记录创建扩容阈值大小的，默认值为0
        if ((sc = sizeCtl) < 0) 
            Thread.yield();	// 竞争初始失败，进行礼让
        // CAS 将 SIZECTL（V） 位置的 预期原值（A）为sc 的值更改为 -1（B）
        else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {	
            try {
                // 三次校验 table为null 或长度为0时执行初始化操作
                if ((tab = table) == null || tab.length == 0) {
                    // 确定初始化容量，若没有指定则为DEFAULT_CAPACITY
                    int n = (sc > 0) ? sc : DEFAULT_CAPACITY;	
                    @SuppressWarnings("unchecked")
                    Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];	// 创建table
                    table = tab = nt;
                    // 计算下次扩容阈值的大小 相当于当前容量的0.75倍
                    sc = n - (n >>> 2);
                }
            } finally {
                sizeCtl = sc;
            }
            break;
        }
    }
    return tab;
}
```

### 扩容

```java
private final void tryPresize(int size) {
    // 要扩至的目标容量大于MAXIMUM_CAPACITY一半，直接扩至最大值，否则调用tableSizeFor获取扩容大小
    int c = (size >= (MAXIMUM_CAPACITY >>> 1)) ? MAXIMUM_CAPACITY :
    tableSizeFor(size + (size >>> 1) + 1);
    int sc;
    // 没有正在初始化或扩容，或table还未初始化
    while ((sc = sizeCtl) >= 0) {
        Node<K,V>[] tab = table; int n;
        // table还未初始化
        if (tab == null || (n = tab.length) == 0) {
            n = (sc > c) ? sc : c;	// 决定初始化大小
            // CAS 将 SIZECTL（V） 位置的 预期原值（A）为sc 的值更改为 -1（B）
            if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
                try {
                    // 再次校验 table 为 null 时执行初始化操作
                    if (table == tab) {
                        @SuppressWarnings("unchecked")
                        Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n]; // 创建table
                        table = nt;
                        // 计算下次扩容阈值的大小 相当于当前容量的0.75倍
                        sc = n - (n >>> 2);
                    }
                } finally {
                    sizeCtl = sc;
                }
            }
        }
        // table容量已达最大或当前线程在到达此处之前已被扩容过
        else if (c <= sc || n >= MAXIMUM_CAPACITY)
            break;
        // 进行扩容
        else if (tab == table) {
            int rs = resizeStamp(n); // 根据容量n生成本次扩容的版本号
            if (sc < 0) {	// 当前有其他线程在进行扩容
                Node<K,V>[] nt;
                // 当前线程无法协助转移数据则退出
                if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                    sc == rs + MAX_RESIZERS || (nt = nextTable) == null ||
                    transferIndex <= 0)
                    break;
                // CAS把正在执行transfer任务的线程数加1，后开始协助数据转移
                if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1))
                    transfer(tab, nt);
            }
            // 无其他线程正在进行扩容 当前线程成为迁移数据的以一个线程
            else if (U.compareAndSwapInt(this, SIZECTL, sc,
                                         (rs << RESIZE_STAMP_SHIFT) + 2))
                transfer(tab, null);
        }
    }
}
```

### 迁移

```java
private final void transfer(Node<K,V>[] tab, Node<K,V>[] nextTab) {
    int n = tab.length, stride;
    //  根据处理机数量 设置当前线程负责迁移数据的数量
    if ((stride = (NCPU > 1) ? (n >>> 3) / NCPU : n) < MIN_TRANSFER_STRIDE)
        stride = MIN_TRANSFER_STRIDE; // 最少16个
    if (nextTab == null) {            // 首次扩容，目标新table为null
        try {
            @SuppressWarnings("unchecked")
            Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];	// 创建新table
            nextTab = nt;
        } catch (Throwable ex) {      // 尝试处理内存溢出
            sizeCtl = Integer.MAX_VALUE;
            return;
        }
        nextTable = nextTab;
        transferIndex = n;	// 当前线程负责迁移的部分初始化为table[n-stride]~table[n -1]
    }
    int nextn = nextTab.length;
    // ForwardingNode节点，旧table的某个位置中的所有节点都迁移完后，用该节点占据当前位置作为标识
    ForwardingNode<K,V> fwd = new ForwardingNode<K,V>(nextTab);
    boolean advance = true;
    boolean finishing = false; // 最后一个迁移线程的finishing为true 保证在nextTable 提交直接进行扫描收尾 
    for (int i = 0, bound = 0;;) {
        Node<K,V> f; int fh;
        while (advance) {	// 旧table当前位置数据迁移完毕
            int nextIndex, nextBound;
            if (--i >= bound || finishing)
                advance = false;
            else if ((nextIndex = transferIndex) <= 0) {
                i = -1;
                advance = false;
            }
            else if (U.compareAndSwapInt
                     (this, TRANSFERINDEX, nextIndex,
                      nextBound = (nextIndex > stride ?
                                   nextIndex - stride : 0))) {
                bound = nextBound;
                i = nextIndex - 1;
                advance = false;
            }
        }
        if (i < 0 || i >= n || i + n >= nextn) {	// 出现扩容冲突或当前是收尾线程
            int sc;
            if (finishing) {	// 是收尾线程
                nextTable = null;
                table = nextTab;	// 将扩容后nextTable赋值给table
                sizeCtl = (n << 1) - (n >>> 1);	// 扩容阈值设为当前容量的0.75
                return;	// 结束for循环
            }
            // 扩容冲突 减少扩容线程数
            if (U.compareAndSwapInt(this, SIZECTL, sc = sizeCtl, sc - 1)) {
                // 如果不是本轮扩容中的最后线程
                if ((sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT)
                    return;	// 直接退出
                finishing = advance = true;	// 将当前线程标记为收尾线程
                i = n; // 将i标记为n保证收尾线程检查旧table所有位置，此时旧table应该都是ForwardingNode
            }
        }
        else if ((f = tabAt(tab, i)) == null)	// 旧table目标位置为null 直接cas尝试放置ForwardingNode
            advance = casTabAt(tab, i, null, fwd);
        else if ((fh = f.hash) == MOVED)	// 旧table目标位置已经完成迁移
            advance = true; // already processed
        else {	// 加锁旧table目标位置进行数据迁移
            synchronized (f) {
                // 由于table的实际容量扩大为旧容量的两倍，之前putVal方法记录元素位置的方法要与上当时table的长度的大小
                // 也就是是说当前table扩容一倍后新的hash码多了一位
                // 只需要根据多的这一位是1或0来判断链表中的元素是否需要变动位置即可
                if (tabAt(tab, i) == f) { // 旧table目标位置为链表
                    Node<K,V> ln, hn;
                    if (fh >= 0) {
                        int runBit = fh & n;	// 与原table容量进行与运算，如果为0则新增位为0
                        Node<K,V> lastRun = f;
                        for (Node<K,V> p = f.next; p != null; p = p.next) {
                            int b = p.hash & n;
                            if (b != runBit) {
                                runBit = b;
                                lastRun = p;
                            }
                        }
                        if (runBit == 0) { // 新增位为0，将其加入低端链表中
                            ln = lastRun;
                            hn = null;
                        }
                        else {	// 新增位为1，将其加入高端链表中
                            hn = lastRun;
                            ln = null;
                        }
                        for (Node<K,V> p = f; p != lastRun; p = p.next) {
                            int ph = p.hash; K pk = p.key; V pv = p.val;
                            if ((ph & n) == 0)
                                ln = new Node<K,V>(ph, pk, pv, ln);
                            else
                                hn = new Node<K,V>(ph, pk, pv, hn);
                        }
                        setTabAt(nextTab, i, ln);	// 将新链表存至两个目标位置
                        setTabAt(nextTab, i + n, hn);
                        setTabAt(tab, i, fwd);	// 旧table目标位置设置ForwardingNode占位
                        advance = true;	// 标记旧table当前位置数据迁移完毕
                    }
                    else if (f instanceof TreeBin) {	// 将红黑树复制到新表中，将红黑树分为高端低端两部分，并判断是否需要还原，使其更好的分散在新容器中 原理同上
                        TreeBin<K,V> t = (TreeBin<K,V>)f;
                        TreeNode<K,V> lo = null, loTail = null;
                        TreeNode<K,V> hi = null, hiTail = null;
                        int lc = 0, hc = 0;
                        for (Node<K,V> e = t.first; e != null; e = e.next) {
                            int h = e.hash;
                            TreeNode<K,V> p = new TreeNode<K,V>
                                (h, e.key, e.val, null, null);
                            if ((h & n) == 0) {
                                if ((p.prev = loTail) == null)
                                    lo = p;
                                else
                                    loTail.next = p;
                                loTail = p;
                                ++lc;
                            }
                            else {
                                if ((p.prev = hiTail) == null)
                                    hi = p;
                                else
                                    hiTail.next = p;
                                hiTail = p;
                                ++hc;
                            }
                        }
                        ln = (lc <= UNTREEIFY_THRESHOLD) ? untreeify(lo) :
                        (hc != 0) ? new TreeBin<K,V>(lo) : t;
                        hn = (hc <= UNTREEIFY_THRESHOLD) ? untreeify(hi) :
                        (lc != 0) ? new TreeBin<K,V>(hi) : t;
                        setTabAt(nextTab, i, ln);
                        setTabAt(nextTab, i + n, hn);
                        setTabAt(tab, i, fwd);
                        advance = true;	// 标记旧table当前位置数据迁移完毕
                    }
                }
            }
        }
    }
}
```

### 协助迁移

```java
final Node<K,V>[] helpTransfer(Node<K,V>[] tab, Node<K,V> f) {
    Node<K,V>[] nextTab; int sc;
    // table不为空 newtable不为空尝试帮助扩容
    if (tab != null && (f instanceof ForwardingNode) &&
        (nextTab = ((ForwardingNode<K,V>)f).nextTable) != null) {
        int rs = resizeStamp(tab.length);	// 根据容量tab.length生成本次扩容的版本号
        // 当前有扩容存在
        while (nextTab == nextTable && table == tab &&
               (sc = sizeCtl) < 0) {
            // 当前线程无法协助转移数据则退出
            // 扩容标识符改变 扩容已经结束 协助线程数量达到最大
            if ((sc >>> RESIZE_STAMP_SHIFT) != rs || sc == rs + 1 ||
                sc == rs + MAX_RESIZERS || transferIndex <= 0)
                break;
            // CAS把正在执行transfer任务的线程数加1，后开始协助数据转移
            if (U.compareAndSwapInt(this, SIZECTL, sc, sc + 1)) {
                // 协助转移
                transfer(tab, nextTab);
                break;
            }
        }
        return nextTab;
    }
    return table;
}
```

