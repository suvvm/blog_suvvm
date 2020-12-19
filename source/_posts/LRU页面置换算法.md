---
title: LRU页面置换算法
date: 2020-04-20 17:26:11
categories: 
- 算法相关
tags:
- 操作系统
- 内存管理
---

# LRU页面置换算法

最近最久未使用（LRU）置换算法

选择最近最久未使用的页面予以淘汰。

## 栈实现

利用栈来保存当前使用的各页面页号。

- 如果栈中没有目标页面且栈还有空余空间，直接将页面标号入栈
- 如果栈中没有目标页面且栈没有空余空间，直接将页面标号入栈，将栈底元素出栈

- 如果栈中有目标页面时，便将该页的页号从栈中移出，将它压入栈顶。

```java
import java.util.Stack;

/**
 * @ClassName: LRU
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/4/20 17:35
 */
public class LRU {
    Stack<Integer> stack;   // 页标栈
    private int capacity;   // 容量
    public int size() {
        return capacity;
    }
    public LRU(int capacity) {
        stack = new Stack<>();
        this.capacity = capacity;
    }
    public void add(int val) {
        if (stack.contains(val)) {  // 栈中存在指定页标
            stack.remove(Integer.valueOf(val)); // 移除目标页标
        } else if (stack.size() == capacity) {  // 容量满
            stack.remove(0);    // 移除栈底
        }
        stack.push(val);    // 指定页标入栈
    }
    public void show() {
        System.out.println(stack);
    }
}

```

测试数据

![img](LRU页面置换算法\LRU栈.JPG)



```java
import org.junit.Test;


/**
 * @ClassName: LRUTest
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/4/20 17:49
 */

public class LRUTest {
    @Test
    public void test() {
        LRU lru = new LRU(5);
        int[] testValues = {4,7,0,7,1,0,1,2,1,2,6};
        for(int i : testValues) {
            lru.add(i);
            lru.show();
        }
    }
}
```

运行结果

```
[4]
[4, 7]
[4, 7, 0]
[4, 0, 7]
[4, 0, 7, 1]
[4, 7, 1, 0]
[4, 7, 0, 1]
[4, 7, 0, 1, 2]
[4, 7, 0, 2, 1]
[4, 7, 0, 1, 2]
[7, 0, 1, 2, 6]
```

## LinkedHashMap实现

LinkedHashMap已经实现了按访问顺序存储，其存在方法removeElfestEntry()用于移除最久未使用数据，极为简便。这个方法将在map的put与putAll方法中被调用，如果返回true就将移除最久未使用数据，原本用于删除过时条目以减少map内存消耗，默认返回false，这里可以利用其特性覆盖该函数实现移除最久未使用数据。

```java
/**
* Returns <tt>true</tt> if this map should remove its eldest entry.
* This method is invoked by <tt>put</tt> and <tt>putAll</tt> after
* inserting a new entry into the map.  It provides the implementor
* with the opportunity to remove the eldest entry each time a new one
* is added.  This is useful if the map represents a cache: it allows
* the map to reduce memory consumption by deleting stale entries.
*
* <p>Sample use: this override will allow the map to grow up to 100
* entries and then delete the eldest entry each time a new entry is
* added, maintaining a steady state of 100 entries.
* <pre>
*     private static final int MAX_ENTRIES = 100;
*
*     protected boolean removeEldestEntry(Map.Entry eldest) {
*        return size() &gt; MAX_ENTRIES;
*     }
* </pre>
*
* <p>This method typically does not modify the map in any way,
* instead allowing the map to modify itself as directed by its
* return value.  It <i>is</i> permitted for this method to modify
* the map directly, but if it does so, it <i>must</i> return
* <tt>false</tt> (indicating that the map should not attempt any
* further modification).  The effects of returning <tt>true</tt>
* after modifying the map from within this method are unspecified.
*
* <p>This implementation merely returns <tt>false</tt> (so that this
* map acts like a normal map - the eldest element is never removed).
*
* @param    eldest The least recently inserted entry in the map, or if
*           this is an access-ordered map, the least recently accessed
*           entry.  This is the entry that will be removed it this
*           method returns <tt>true</tt>.  If the map was empty prior
*           to the <tt>put</tt> or <tt>putAll</tt> invocation resulting
*           in this invocation, this will be the entry that was just
*           inserted; in other words, if the map contains a single
*           entry, the eldest entry is also the newest.
* @return   <tt>true</tt> if the eldest entry should be removed
*           from the map; <tt>false</tt> if it should be retained.
*/
protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return false;
}
```

实现代码

```java
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * @ClassName: LRULinkedHashMap
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/4/20 18:02
 */
public class LRULinkedHashMap {
    private LinkedHashMap<Integer, Object> map;
    private int capacity;

    public LRULinkedHashMap(int capacity) {
        map = new LinkedHashMap<Integer, Object>() {
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return size() > LRULinkedHashMap.this.capacity;
            }
        };
        this.capacity = capacity;
    }

    public void add(int val) {
        map.put(val, new Object());
    }
    public void show() {
        System.out.print("[");
        int cnt = 0;
        for (Map.Entry<Integer, Object> i : map.entrySet()) {
            if (cnt != 0)
                System.out.print(", ");
            cnt++;
            System.out.print(i.getKey());
        }
        System.out.println("]");
    }
}
```

测试数据

![img](LRU页面置换算法\LRU栈.JPG)

```java
import org.junit.Test;


/**
 * @ClassName: LRUTest
 * @Description: TODO
 * @Author: SUVVM
 * @Date: 2020/4/20 17:49
 */

public class LRUTest {
//    @Test
//    public void test() {
//        LRU lru = new LRU(5);
//        int[] testValues = {4,7,0,7,1,0,1,2,1,2,6};
//        for(int i : testValues) {
//            lru.add(i);
//            lru.show();
//        }
//    }
    @Test
    public void testLinkedHashMap() {
        LRULinkedHashMap lru = new LRULinkedHashMap(5);
        int[] testValues = {4,7,0,7,1,0,1,2,1,2,6};
        for(int i : testValues) {
            lru.add(i);
            lru.show();
        }
    }
}
```

运行结果

```
[4]
[4, 7]
[4, 7, 0]
[4, 7, 0]
[4, 7, 0, 1]
[4, 7, 0, 1]
[4, 7, 0, 1]
[4, 7, 0, 1, 2]
[4, 7, 0, 1, 2]
[4, 7, 0, 1, 2]
[7, 0, 1, 2, 6]
```

