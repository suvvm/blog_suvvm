---
title: 红黑树与Java HashMap
date: 2020-04-04 15:59:46
categories: 
- Java
tags:
- HashMap
- 红黑树
---

# 红黑树与Java HashMap

## 红黑树原理

### 红黑树性质

红黑树是一颗区分结点颜色的二叉搜索树，与AVL树对平衡的吹毛求疵不同，红黑树想要实现的是不完全平衡。

- 红黑树结点分为红色结点与黑色结点
- 根节点必定为黑色结点
- 叶子节点（空值）必定为黑色结点
- 不存在两个相邻的红色结点
- 任一结点到每个叶子节点的路径中经过的黑色结点数量相同

根据以上特性，红黑树并不为何一条路径上所有的结点都平衡，第5条决定了没有路径能多于任何其他路径的两倍长，一棵有n个内结点的红黑树的高度之多为2lg(n+1)。

### 红黑树查找

红黑树的查找等同于二叉搜索树的查找

### 红黑树的插入

- 如同二叉搜素树一般查找到插入的目标位置，在目标位置插入结点
- 新插入结点为红结点
- 插入根节点无需平衡
- 父节点黑色直接插入
- 父节点红色（祖父结点一定存在）
  - 祖父黑、父与叔红标黑父与叔标红祖父后祖父作为插入点继续平衡
  - 父节点为左子结点、叔结点不存在或者为黑色
    - 插入点是其父的左子结点，将父亲标黑祖父标红，右旋祖父
    - 插入点是其父的右子结点，对其父进行左旋，将其父作为插入点则回到上方情况
  - 父节点为右子结点、叔结点不存在或者为黑色
    - 插入点是其父的右子结点，将父亲标黑祖父标红，左旋祖父
    - 插入点是其父的左子结点，对其父进行右旋，将其父作为插入点则回到上方情况

### 红黑树的删除

- 如同二叉搜索树删除一般，若非删除叶子节点单孩子直接上提，多孩子则寻找直接后继结点作为替代结点覆盖当前欲删除结点。

- 要删除结点为红色，直接改为黑色即可，不会影响树的平衡

- 删除结点为黑色

  - 删除节点是其父节点的左子结点

    - 删除节点的兄弟是红结点，父节点兄弟结点的子结点必定都为黑色，将兄弟结点设为黑色，将父节点设为红色（兄父颜色互换），对父节点左旋，转为下方兄弟是黑结点情况处理。

    - 删除的兄弟结点是黑结点

      - 兄弟节点的右子结点为红结点（最远侄子结点）

        将父节点与兄弟结点的颜色互换后进行左旋，这时兄弟结点变为了父节点，将曾经的远侄结点标黑后直接删除要删除节点即可

      - 兄弟节点的右子结点为黑结点（最远侄子结点）

        - 兄弟节点的左子结点为红色（最近侄子结点）

          对兄弟结点进行右旋操作并交换最近侄子结点与兄弟结点的颜色，最近侄子结点成为兄弟结点，转换为了上述最远侄子结点为红色的情况

      - 兄弟节点的左右子结点都为黑结点，且父节点为红色

        将父节点改为黑色，兄弟结点改为红色后删除要删除结点即可

      - 兄弟节点的左右子结点都为黑结点，且父节点为黑色

        将兄弟结点改为红色，对父节点进行平衡操作

  - 删除节点是其父节点的右子结点

    - 基本为删除节点是其父节点的左子结点的镜像操作



## HashMap中的红黑树

### 结点结构

HashMap中的红黑树节点TreeNode继承自LinkedHashMap.Entry，LinkedHashMap.Entry又继承自HashMap.Node

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```

LinkedHashMap.Entry

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }
```

TreeNode

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // 父节点
    TreeNode<K,V> left;	// 左子结点
    TreeNode<K,V> right;	// 右子节点
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;	// 颜色
    TreeNode(int hash, K key, V val, Node<K,V> next) {...}
    // 该方法将返回包含此节点的树的根节点
    final TreeNode<K,V> root() {...}
    static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {...}
    final TreeNode<K,V> find(int h, Object k, Class<?> kc) {...}
    final TreeNode<K,V> getTreeNode(int h, Object k) {...}
    static int tieBreakOrder(Object a, Object b) {...}
    final void treeify(Node<K,V>[] tab) {...}
    final Node<K,V> untreeify(HashMap<K,V> map) {...}
    final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab, int h, K k, V v) {...}
    final void removeTreeNode(HashMap<K,V> map, Node<K,V>[] tab, boolean movable) {...}
    final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit) {...}
    static <K,V> TreeNode<K,V> rotateLeft(TreeNode<K,V> root, TreeNode<K,V> p) {...}
    static <K,V> TreeNode<K,V> rotateRight(TreeNode<K,V> root, TreeNode<K,V> p) {...}
    static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root, TreeNode<K,V> x) {...}
    static <K,V> TreeNode<K,V> balanceDeletion(TreeNode<K,V> root, TreeNode<K,V> x) {...}
    static <K,V> boolean checkInvariants(TreeNode<K,V> t) {...}
}
```

### 构造器

```java
TreeNode(int hash, K key, V val, Node<K,V> next) {
    super(hash, key, val, next);
}
```

### 获取根节点

```java
final TreeNode<K,V> root() {
    for (TreeNode<K,V> r = this, p;;) {
        if ((p = r.parent) == null)	// 循环为p赋值为当前结点的父节点直到找到根节点后将其返回
            return r;
        r = p;
    }
}
```

### 将树的根节点移至tab对应下标的首位

```java
static <K,V> void moveRootToFront(Node<K,V>[] tab, TreeNode<K,V> root) {
    int n;
    if (root != null && tab != null && (n = tab.length) > 0) {
        int index = (n - 1) & root.hash;	// 找到root结点位置的下标
        TreeNode<K,V> first = (TreeNode<K,V>)tab[index];	// 获取下标位置首位结点
        if (root != first) {	// 根节点不在首位
            Node<K,V> rn;	
            tab[index] = root;	// 链表首结点记为root
            TreeNode<K,V> rp = root.prev;	// 记录root前方的结点
            if ((rn = root.next) != null)	// root后方有其他结点
                ((TreeNode<K,V>)rn).prev = rp;	// 将后方的第一个结点强转为TreeNode并将其前方的结点记为root前方的结点
            if (rp != null)	// 将root前方结点的下一个结点指向root的下一个结点
                rp.next = rn;
            if (first != null)	// 将原链表头结点的前一个结点记为root
                first.prev = root;
            root.next = first;	// 将root的下一结点记为原链表头结点
            root.prev = null;	// 将root前方的结点记为null
        }
        assert checkInvariants(root);	// 断言根节点合法
    }
}
```

### 检查结点是否合法

```java
static <K,V> boolean checkInvariants(TreeNode<K,V> t) {
    TreeNode<K,V> tp = t.parent, tl = t.left, tr = t.right,
    tb = t.prev, tn = (TreeNode<K,V>)t.next;
    if (tb != null && tb.next != t)	// t前方有结点但前方结点的下一个结点不为t
        return false;
    if (tn != null && tn.prev != t)	// t后方有结点但后方结点的前方结点不为t
        return false;
    if (tp != null && t != tp.left && t != tp.right)	// t有父节点t但父节点的左右字节点都不是t
        return false;
    if (tl != null && (tl.parent != t || tl.hash > t.hash))	// t有左子结点但是t的左子节点的父节点不为t或t的左子节点的哈希值大于t的哈希值
        return false;
    if (tr != null && (tr.parent != t || tr.hash < t.hash))	// t有右子结点但是t的右子节点的父节点不为t或t的右子节点的哈希值小于t的哈希值
        return false;
    if (t.red && tl != null && tl.red && tr != null && tr.red)	// t为红色且t的左右子结点都为红色
        return false;
    if (tl != null && !checkInvariants(tl))	// t的左子结点不合法
        return false;
    if (tr != null && !checkInvariants(tr))	// t的右子节点不合法
        return false;
    return true;
}
```

### 查找函数

从当前结点开始查找具有给定哈希值和key的节点

```java
final TreeNode<K,V> find(int h, Object k, Class<?> kc) {
    TreeNode<K,V> p = this;	// p代表当前查询结点
    do {
        int ph, dir; K pk;
        TreeNode<K,V> pl = p.left, pr = p.right, q;
        if ((ph = p.hash) > h) // 当前结点哈希值大于给定哈希值
            p = pl;	// p设为左子节点查找左子树
        else if (ph < h) // 当前结点哈希值小于给定哈希值
            p = pr;	// p设为左子节点查找左子树
        else if ((pk = p.key) == k || (k != null && k.equals(pk)))	// 当前结点的键与给定键引用了同一个对象或当前结点键与给定键逻辑相等返回当前结点
            return p;
        else if (pl == null)	// 左子结点为空
            p = pr;	// 去搜索右子树
        else if (pr == null)	// 右子节点为空
            p = pl;	// 去搜索左子树
        else if ((kc != null ||
                  (kc = comparableClassFor(k)) != null) &&
                 (dir = compareComparables(kc, k, pk)) != 0)	// 若k的类实现了Comparable接口kc在第一次比较时缓存为k的运时类型，后比较当前结点键与给定键的大小
            p = (dir < 0) ? pl : pr;	// 如果当前小于给定key去搜索左子树否则搜索右子树
        else if ((q = pr.find(h, k, kc)) != null)	// 如果在右子树中找到目标就返回
            return q;
        else
            p = pl;
    } while (p != null);
    return null;
}
```

由根节点开始查找具有给定哈希值和key的节点

```java
final TreeNode<K,V> getTreeNode(int h, Object k) {
    return ((parent != null) ? root() : this).find(h, k, null);
}
```

### 树化函数

```java
final void treeify(Node<K,V>[] tab) {
    TreeNode<K,V> root = null;
    for (TreeNode<K,V> x = this, next; x != null; x = next) {	// 遍历链表将对应结点加入树中
        next = (TreeNode<K,V>)x.next; // 记录x的下一节点
        x.left = x.right = null;	// x左右子树初始化为null
        if (root == null) {	// 如果根节点为null 将x作为根节点
            x.parent = null;
            x.red = false;
            root = x;
        }
        else {	// 根节点不为空
            K k = x.key;	// 记录x的键
            int h = x.hash;	// 记录x的哈希值
            Class<?> kc = null;	// k的运行时类型初始化为null
            for (TreeNode<K,V> p = root;;) {
                int dir, ph;
                K pk = p.key;	// 记录当前查找点的键
                if ((ph = p.hash) > h)	// 若要加入树中的结点的哈希值大于当前结点的哈希值
                    dir = -1;	
                else if (ph < h)	// 若要加入树中的结点的哈希值小于当前结点的哈希值
                    dir = 1;
                else if ((kc == null &&
                          (kc = comparableClassFor(k)) == null) ||
                         (dir = compareComparables(kc, k, pk)) == 0)	// 若要加入树中的结点的哈希值等于当前结点的哈希值
                    dir = tieBreakOrder(k, pk);	// 比较内存地址

                TreeNode<K,V> xp = p;
                if ((p = (dir <= 0) ? p.left : p.right) == null) {	// 要插入的位置为null值插入对应位置后进行插入平衡操作
                    x.parent = xp;
                    if (dir <= 0)
                        xp.left = x;
                    else
                        xp.right = x;
                    root = balanceInsertion(root, x);
                    break;
                }
            }
        }
    }
    moveRootToFront(tab, root);	// 键树完毕后将树的根节点移至tab对应下标的首位
}
```

### 树还原函数

返回还原后的链表头结点

```java
final Node<K,V> untreeify(HashMap<K,V> map) {
    Node<K,V> hd = null, tl = null;
    for (Node<K,V> q = this; q != null; q = q.next) {
        Node<K,V> p = map.replacementNode(q, null);	// 以结点p创建一个新的链表结点
        if (tl == null)	// 头结点
            hd = p;
        else
            tl.next = p;
        tl = p;
    }
    return hd;	
}
```

### 向红黑树中添加结点

```java
final TreeNode<K,V> putTreeVal(HashMap<K,V> map, Node<K,V>[] tab, int h, K k, V v) {
    Class<?> kc = null;
    boolean searched = false;
    TreeNode<K,V> root = (parent != null) ? root() : this;	// 获取树根节点
    for (TreeNode<K,V> p = root;;) {
        int dir, ph; K pk;
        if ((ph = p.hash) > h)	// 若要加入树中的结点的哈希值大于当前结点的哈希值
            dir = -1;
        else if (ph < h)	// 若要加入树中的结点的哈希值小于当前结点的哈希值
            dir = 1;
        else if ((pk = p.key) == k || (k != null && k.equals(pk)))	// 哈希值等于当前结点且当前结点键与要加入的键是同一个对象或者相等，直接返回当前结点
            return p;
        else if ((kc == null &&
                  (kc = comparableClassFor(k)) == null) ||
                 (dir = compareComparables(kc, k, pk)) == 0) {
            if (!searched) {
                TreeNode<K,V> q, ch;
                searched = true;	
                // 寻找p的子树中是否包含要加入的点
                if (((ch = p.left) != null &&
                     (q = ch.find(h, k, kc)) != null) ||
                    ((ch = p.right) != null &&
                     (q = ch.find(h, k, kc)) != null))
                    return q;
            }
            dir = tieBreakOrder(k, pk);	// 比较内存地址
        }

        TreeNode<K,V> xp = p;
        if ((p = (dir <= 0) ? p.left : p.right) == null) {	// 找到对应插入位置并进行插入操作
            Node<K,V> xpn = xp.next;
            TreeNode<K,V> x = map.newTreeNode(h, k, v, xpn);
            if (dir <= 0)
                xp.left = x;
            else
                xp.right = x;
            xp.next = x;
            x.parent = x.prev = xp;
            if (xpn != null)
                ((TreeNode<K,V>)xpn).prev = x;
            moveRootToFront(tab, balanceInsertion(root, x));	// 指向插入平衡函数并将根结点移至tab对应下标的首位节点
            return null;
        }
    }
}
```

### 从红黑树中删除点

- 若被删除点无子结点直接删除
- 若被删除点只有一个子结点就以子结点替换删除结点
- 若被删除结点有左右子树，以右子树的最左结点（大于被删除结点的最小结点）替换被删除点，也可以用左子树最右结点替换（小于被删除结点的最大结点）

```java
final void removeTreeNode(HashMap<K,V> map, Node<K,V>[] tab, boolean movable) {
    int n;
    if (tab == null || (n = tab.length) == 0)	// 没有要删除的结点
        return;
    int index = (n - 1) & hash;	// 取得当前结点的下标
    TreeNode<K,V> first = (TreeNode<K,V>)tab[index], root = first, rl;
    TreeNode<K,V> succ = (TreeNode<K,V>)next, pred = prev;
    if (pred == null)	// 要删除的当前点为链表首
        tab[index] = first = succ;	// 将链表首记为下一个点
    else
        pred.next = succ;	// 将链表前一个点的下一个点记要删除点的下一个点
    if (succ != null)	// 要删除的不链表尾
        succ.prev = pred;	// 要删除结点后一个点的前一个点记为要删除点的前一个点
    if (first == null)	// 要删除的点不存在
        return;
    if (root.parent != null)	// 链表首不是根节点
        root = root.root();	// 获取根节点
    if (root == null
        || (movable
            && (root.right == null
                || (rl = root.left) == null
                || rl.left == null))) {	// 
        tab[index] = first.untreeify(map);  // 树太小需要还原为链表
        return;
    }
    TreeNode<K,V> p = this, pl = left, pr = right, replacement;
    if (pl != null && pr != null) {	// 要删除结点有左右子树
        TreeNode<K,V> s = pr, sl;
        while ((sl = s.left) != null) // 找到右子树中最左结点
            s = sl;
        boolean c = s.red; s.red = p.red; p.red = c; // 将右子树中最左结点的颜色与当前要删除点的颜色互换
        TreeNode<K,V> sr = s.right;
        TreeNode<K,V> pp = p.parent;
        if (s == pr) { //要删除点的右子树只有s一个点
            p.parent = s;	//将要删除点作为s的右子节点
            s.right = p;
        }
        else {
            TreeNode<K,V> sp = s.parent;	// 记录右子树最左结点的父节点
            if ((p.parent = sp) != null) {	// 将要删除的结点放置在原右子树最左结点的位置
                if (s == sp.left)
                    sp.left = p;
                else
                    sp.right = p;
            }
            if ((s.right = pr) != null)	// 将原p的右子树交给s
                pr.parent = s;
        }
        // 交换要删除结点p与s的位置
        p.left = null;
        if ((p.right = sr) != null)	
            sr.parent = p;
        if ((s.left = pl) != null)
            pl.parent = s;
        if ((s.parent = pp) == null)
            root = s;
        else if (p == pp.left)
            pp.left = s;
        else
            pp.right = s;
        if (sr != null)
            replacement = sr;
        else
            replacement = p;
    }
    else if (pl != null)	// 被删除结点有左子树没有右子树
        replacement = pl;
    else if (pr != null)	// 被删除结点有右子树没有左子树
        replacement = pr;
    else	// 被删除的结点没有左右子树
        replacement = p;
    if (replacement != p) {	// p有子结点，要替换的点非p 用要替换的点替换掉原p位置并干掉p
        TreeNode<K,V> pp = replacement.parent = p.parent;
        if (pp == null)	 
            root = replacement;
        else if (p == pp.left)
            pp.left = replacement;
        else
            pp.right = replacement;
        p.left = p.right = p.parent = null;
    }

    TreeNode<K,V> r = p.red ? root : balanceDeletion(root, replacement);	// 若p是红色不需要调平衡若是黑色就进行删除平衡操作

    if (replacement == p) {  // p没有左右子树，可以直接干掉
        TreeNode<K,V> pp = p.parent;
        p.parent = null;	// 干掉p
        if (pp != null) {
            if (p == pp.left)
                pp.left = null;
            else if (p == pp.right)
                pp.right = null;
        }
    }
    if (movable)	// 将新的root移到首位
        moveRootToFront(tab, r);
}
```

### resize后将红黑树复制到新表中的split函数

```java
 final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit) {
     TreeNode<K,V> b = this;
     // 将红黑树分为高端低端两部分 使其更好的分散在新容器中
     TreeNode<K,V> loHead = null, loTail = null;
     TreeNode<K,V> hiHead = null, hiTail = null;
     int lc = 0, hc = 0;
     for (TreeNode<K,V> e = b, next; e != null; e = next) {	// 链表遍历
         // bit为扩容前容量
         next = (TreeNode<K,V>)e.next;
         e.next = null;
         if ((e.hash & bit) == 0) {	// lo串索引与原先相同
             if ((e.prev = loTail) == null)
                 loHead = e;
             else
                 loTail.next = e;
             loTail = e;
             ++lc;
         }
         else {	// hi串新索引
             if ((e.prev = hiTail) == null)
                 hiHead = e;
             else
                 hiTail.next = e;
             hiTail = e;
             ++hc;
         }
     }
	// 拆分完毕后存在低端
     if (loHead != null) {
         if (lc <= UNTREEIFY_THRESHOLD)	// 树的结点小于还原阈值，将树还原为链表
             tab[index] = loHead.untreeify(map);
         else {
             tab[index] = loHead;
             if (hiHead != null) // 将链表转为红黑树
                 loHead.treeify(tab);
         }
     }
     // 拆分完毕后存在高端
     if (hiHead != null) {
         if (hc <= UNTREEIFY_THRESHOLD)	// 还原
             tab[index + bit] = hiHead.untreeify(map);
         else {
             tab[index + bit] = hiHead;
             if (loHead != null)	// 树化
                 hiHead.treeify(tab);
         }
     }
 }
```

### 旋转函数

#### 左旋

```java
static <K,V> TreeNode<K,V> rotateLeft(TreeNode<K,V> root, TreeNode<K,V> p) {
    TreeNode<K,V> r, pp, rl;
    if (p != null && (r = p.right) != null) {	// 要左旋转的结点p有右子树
        if ((rl = p.right = r.left) != null)	// 要左旋的结点p的右子树有左子树
            rl.parent = p;	// 将p右子树的左子树的父结点记为p
        if ((pp = r.parent = p.parent) == null)	// 若p为根结点
            (root = r).red = false;	// 将p的右子结点向左旋转为根结点 并标为黑色
        else if (pp.left == p)	// 若p是其父亲的左子结点
            pp.left = r;	// 将旋转后的r（p右子结点）代替p
        else
            pp.right = r;	// 将旋转后的r（p右子结点）代替p
        r.left = p;	// 将p旋转为r的左子树
        p.parent = r;
    }
    return root;
}
```

#### 右旋

```java
static <K,V> TreeNode<K,V> rotateRight(TreeNode<K,V> root, TreeNode<K,V> p) {
    TreeNode<K,V> l, pp, lr;
    if (p != null && (l = p.left) != null) {	// 要右旋转的结点p有左子树
        if ((lr = p.left = l.right) != null)	// 要右旋的结点p的左子树有右子树
            lr.parent = p;	// 将p左子树的右子树的父结点记为p
        if ((pp = l.parent = p.parent) == null)	// 若p为根结点
            (root = l).red = false;	// 将p的左子结点向右旋转为根结点 并标为黑色
        else if (pp.right == p)	// 若p是其父亲的右子结点
            pp.right = l;
        else	// 若p是其父亲的左子结点
            pp.left = l;
        l.right = p;	// 将p旋转为r的右子树
        p.parent = l;
    }
    return root;
}
```

### 平衡函数

#### 插入平衡

平衡规则

- 插入根节点无需平衡
- 父节点黑色直接插入
- 父节点红色（祖父结点一定存在）
  - 祖父黑、父与叔红标黑父与叔标红祖父后祖父作为插入点继续平衡
  - 父节点为左子结点、叔结点不存在或者为黑色
    - 插入点是其父的左子结点，将父亲标黑祖父标红，右旋祖父
    - 插入点是其父的右子结点，对其父进行左旋，将其父作为插入点则回到上方情况
  - 父节点为右子结点、叔结点不存在或者为黑色
    - 插入点是其父的右子结点，将父亲标黑祖父标红，左旋祖父
    - 插入点是其父的左子结点，对其父进行右旋，将其父作为插入点则回到上方情况

```java
static <K,V> TreeNode<K,V> balanceInsertion(TreeNode<K,V> root, TreeNode<K,V> x) {
    x.red = true;	// 将新增要平衡点表红
    for (TreeNode<K,V> xp, xpp, xppl, xppr;;) {
        if ((xp = x.parent) == null) {	// 要平衡点为根结点
            x.red = false;	// 标黑
            return x;
        }
        else if (!xp.red || (xpp = xp.parent) == null)	// x父节点为黑色或者x父结点为根结点
            return root;
        if (xp == (xppl = xpp.left)) {	// x父节点为祖父结点的左子结点
            if ((xppr = xpp.right) != null && xppr.red) {	// x祖父结点的右子结点（叔结点）为红色
                xppr.red = false;	// 叔结点标黑
                xp.red = false;	// 父节点标黑
                xpp.red = true;	// 祖父结点表红
                x = xpp;	// 需平衡点x记为祖父结点
            }
            else {	// x祖父结点的右子结点（叔结点）不为红色或者为空
                if (x == xp.right) {	// 若x为其父的右子结点
                    root = rotateLeft(root, x = xp);	// x父结点左旋后需平衡点x记为父结点（）
                    xpp = (xp = x.parent) == null ? null : xp.parent;	// 获取现在x的祖父节点
                }
                if (xp != null) {	// 当前x不为根节点
                    xp.red = false;	// 标黑
                    if (xpp != null) {	// x父节点不为根节点
                        xpp.red = true;	// 祖父结点标红
                        root = rotateRight(root, xpp);	// 右旋x的祖父
                    }
                }
            }
        }
        else {	// 父节点为右子结点
            if (xppl != null && xppl.red) {	// 叔结点为红色
                xppl.red = false;
                xp.red = false;
                xpp.red = true;
                x = xpp;
            }
            else {	// 叔结点不存在或者为黑色
                if (x == xp.left) {
                    root = rotateRight(root, x = xp);
                    xpp = (xp = x.parent) == null ? null : xp.parent;
                }
                if (xp != null) {
                    xp.red = false;
                    if (xpp != null) {
                        xpp.red = true;
                        root = rotateLeft(root, xpp);
                    }
                }
            }
        }
    }
}
```

#### 删除平衡（仅限删除结点为黑色）

平衡原则

- 替换结点为红色，直接改为黑色即可，不会影响树的平衡

- 替换结点为黑色

  - 替换节点是其父节点的左子结点

    - 替换节点的兄弟是红结点，父节点兄弟结点的子结点必定都为黑色，将兄弟结点设为黑色，将父节点设为红色（兄父颜色互换），对父节点左旋，转为下方兄弟是黑结点情况处理。

    - 替换节点的兄弟结点是黑结点

      - 兄弟节点的右子结点为红结点（最远侄子结点）

        将父节点与兄弟结点的颜色互换后进行左旋，这时兄弟结点变为了父节点，将曾经的远侄结点标黑后直接删除替换节点即可

      - 兄弟节点的右子结点为黑结点（最远侄子结点）

        - 兄弟节点的左子结点为红色（最近侄子结点）

          对兄弟结点进行右旋操作并交换最近侄子结点与兄弟结点的颜色，最近侄子结点成为兄弟结点，转换为了上述最远侄子结点为红色的情况

      - 兄弟节点的左右子结点都为黑结点，且父节点为红色

        将父节点改为黑色，兄弟结点改为红色后删除替换结点即可

      - 兄弟节点的左右子结点都为黑结点，且父节点为黑色

        将兄弟结点改为红色，对父节点进行平衡操作

  - 替换节点是其父节点的右子结点

    - 基本为替换节点是其父节点的左子结点的镜像操作

```java
static <K,V> TreeNode<K,V> balanceDeletion(TreeNode<K,V> root, TreeNode<K,V> x) {
    for (TreeNode<K,V> xp, xpl, xpr;;) {
        if (x == null || x == root)	
            return root;
        else if ((xp = x.parent) == null) {	// 平衡点为根节点
            x.red = false;	// 标黑
            return x;
        }
        else if (x.red) {	// 平衡点为红色结点不会改为黑色不会影响平衡性
            x.red = false;	// 标黑
            return root;
        }
        else if ((xpl = xp.left) == x) {	// x为其父的左子结点
            if ((xpr = xp.right) != null && xpr.red) {	// x兄弟结点为红结点
                xpr.red = false;	// 兄弟结点标黑
                xp.red = true;	// 父节点标
                root = rotateLeft(root, xp);	// 对父节点左旋
                xpr = (xp = x.parent) == null ? null : xp.right;	// 获取旋转后的兄弟结点 
            }
            if (xpr == null)	// 兄弟结点不存在
                x = xp;	// x记为x的父节点
            else {	// 兄弟结点为黑结点
                TreeNode<K,V> sl = xpr.left, sr = xpr.right;
                if ((sr == null || !sr.red) &&
                    (sl == null || !sl.red)) {	// 兄弟结点的右子结点为黑或者空且兄弟结点的左子结点也为黑或空（兄弟不存在红色子结点）
                    xpr.red = true;	// 兄弟结点标红
                    x = xp;	// 要x记为x父节点
                }
                else {	// 兄弟存在红色子结点
                    if (sr == null || !sr.red) {	// 兄弟的右子结点为黑或空 
                        if (sl != null)	// 左子结点标黑
                            sl.red = false;
                        xpr.red = true;	// 兄弟结点标红
                        root = rotateRight(root, xpr);	// 兄弟结点右旋
                        xpr = (xp = x.parent) == null ?
                            null : xp.right;	// 获取旋转后的兄弟结点
                    }
                    if (xpr != null) {	// 兄弟结点不为空
                        xpr.red = (xp == null) ? false : xp.red;	// 兄弟结点标为与父节点同色
                        if ((sr = xpr.right) != null)	// 兄弟结点右子结点不为空
                            sr.red = false;	// 右子结点标黑
                    }
                    if (xp != null) {	// 存在父节点
                        xp.red = false;	// 父节点标黑
                        root = rotateLeft(root, xp);	// 父节点左旋
                    }
                    x = root;
                }
            }
        }
        else { // 与上述操作对称
            if (xpl != null && xpl.red) {
                xpl.red = false;
                xp.red = true;
                root = rotateRight(root, xp);
                xpl = (xp = x.parent) == null ? null : xp.left;
            }
            if (xpl == null)
                x = xp;
            else {
                TreeNode<K,V> sl = xpl.left, sr = xpl.right;
                if ((sl == null || !sl.red) &&
                    (sr == null || !sr.red)) {
                    xpl.red = true;
                    x = xp;
                }
                else {
                    if (sl == null || !sl.red) {
                        if (sr != null)
                            sr.red = false;
                        xpl.red = true;
                        root = rotateLeft(root, xpl);
                        xpl = (xp = x.parent) == null ?
                            null : xp.left;
                    }
                    if (xpl != null) {
                        xpl.red = (xp == null) ? false : xp.red;
                        if ((sl = xpl.left) != null)
                            sl.red = false;
                    }
                    if (xp != null) {
                        xp.red = false;
                        root = rotateRight(root, xp);
                    }
                    x = root;
                }
            }
        }
    }
}
```

### 其他工具函数

#### 在哈希码相等时比较以比较地址代替

```java
static int tieBreakOrder(Object a, Object b) {
    int d;
    if (a == null || b == null ||
        (d = a.getClass().getName().
         compareTo(b.getClass().getName())) == 0)	// a或b为null或两者类型不一致
        d = (System.identityHashCode(a) <= System.identityHashCode(b) ?
             -1 : 1);
    return d;
}
```

#### 检查结点合法性

```java
static <K,V> boolean checkInvariants(TreeNode<K,V> t) {
    TreeNode<K,V> tp = t.parent, tl = t.left, tr = t.right,
    tb = t.prev, tn = (TreeNode<K,V>)t.next;
    if (tb != null && tb.next != t)
        return false;
    if (tn != null && tn.prev != t)
        return false;
    if (tp != null && t != tp.left && t != tp.right)
        return false;
    if (tl != null && (tl.parent != t || tl.hash > t.hash))
        return false;
    if (tr != null && (tr.parent != t || tr.hash < t.hash))
        return false;
    if (t.red && tl != null && tl.red && tr != null && tr.red)
        return false;
    if (tl != null && !checkInvariants(tl))
        return false;
    if (tr != null && !checkInvariants(tr))
        return false;
    return true;
}
```

