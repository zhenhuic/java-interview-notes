[toc]

# HashMap

## 数据结构

### Node结构

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
```

### HashMap结构

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
    static final int MAXIMUM_CAPACITY = 1 << 30;
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
    /* ---------------- Fields -------------- */
    transient Node<K,V>[] table;
    transient Set<Map.Entry<K,V>> entrySet;
    transient int size;
    transient int modCount;
    int threshold;
    final float loadFactor;
}
```

### 红黑树结构

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
    TreeNode<K,V> parent;  // red-black tree links
    TreeNode<K,V> left;
    TreeNode<K,V> right;
    TreeNode<K,V> prev;    // needed to unlink next upon deletion
    boolean red;
}
```

## get查询

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key);
```

1. 依次比较key的hash、引用地址（用==判断）、equals方法是否相等，如果相等就返回。

2. 如果不相等，那么判断是不是链表或者红黑树。如果是链表就依次遍历链表节点比对，如果是红黑树，就执行`((TreeNode<K,V>)first).getTreeNode(hash, key);`。

## 计算hash值

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

用高16位于低16位做异或（XOR），降低hash碰撞的概率，这是因为由于哈希表的长度有限所以高位的hash比特位是用不到的。用这种方式是在执行速度、实用性和扩展的质量上的权衡。

## put添加元素

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict);
```

1. 如果table为null或者长度为0，执行扩容`resize()`；

2. 根据`(n - 1) & hash`得到索引查找到哈希表的对应位置是否为空:

   - 如果为空，创建新的节点，将节点放入该位置。

   - 如果该位置不为空：

       - 比对第一个节点的key是否一致，如果一致将该节点赋给局部变量`Node<K,V> e`。

       - 如果该位置节点是`TreeNode`，这执行`e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);`。

       - 如果是个链表，遍历链表，并记录链表上节点的个数，如果找到跟要添加的key一致的节点，就将该节点赋给变量`e`，如果没有找到，就创建一个新的节点插到尾部。

3. 如果哈希表中已经存在跟要添加的key一样的节点`if (e != null)`，那么更改节点的值，执行`afterNodeAccess(e);`，返回旧值。

4. 如果哈希表中原来不存在要添加的key的节点，那么才执行`++modCount;`；如果size大于阈值，就执行扩容；最后执行`afterNodeInsertion(evict);`，返回null（因为没有旧值）。

## 初始化或扩容

```java
final Node<K,V>[] resize();
```

1. 先计算出新的容量`newCap`和新的阈值`newThr`：

   - 如果是初始化且没有设置容量，初始化就用`DEFAULT_INITIAL_CAPACITY = 1 << 4`，新的threshold等于`(int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY)`；

   - 如果初始化且设置了初始化容量（HashMap中没有设置保存总容量的的字段，设置的初始化容量一开始是赋给threshold变量的），那么设置`newCap`，计算出新的`newThr`；

   - 如果是扩容，那么新的容量和阈值都变为原来的2倍，这里如果原来的容量大于`MAXIMUM_CAPACITY`，那么阈值设为最大整型数值。

2. 创建新的哈希数组，然后遍历就旧数组做rehash：

   - 如果该位置上只有一个节点，那么直接`newTab[e.hash & (newCap - 1)] = e;`；

   - 如果是个链表，因为新的数组大小是旧数值的两倍，所以某个节点rehash后要么还是落在原来索引位置，要么就是落在原来索引+oldCap的位置。然后用**尾插法**来迁移链表中的节点，所以设置了高位和低位的头尾节点，再利用`(e.hash & oldCap) == 0`计算出新加的那一位是0还是1来加到高位还是低位的尾节点后面。最后再将高低位的头节点分别放入数组的对应位置。

   - 如果该位置是个`TreeNode`，进行树的分裂`final void split(HashMap<K,V> map, Node<K,V>[] tab, int index, int bit)`。

## 红黑树

HashMap中实现的红黑树，首先是比较节点的hash值，其次如果实现了`Comparable`接口的就用`compareTo()`比较，如果都相等或者没有实现`Comparable`接口，就执行`static int tieBreakOrder(Object a, Object b)`打破僵局。
