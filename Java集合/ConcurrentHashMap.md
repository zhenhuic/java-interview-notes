[toc]

# ConcurrentHashMap

## ConcurrentHashMap结构

```java
public class ConcurrentHashMap<K,V> extends AbstractMap<K,V> implements ConcurrentMap<K,V>, Serializable {
    /* ---------------- Constants -------------- */
    private static final int MAXIMUM_CAPACITY = 1 << 30;
    private static final int DEFAULT_CAPACITY = 16;
    static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
    private static final int DEFAULT_CONCURRENCY_LEVEL = 16;
    private static final float LOAD_FACTOR = 0.75f;
    static final int TREEIFY_THRESHOLD = 8;
    static final int UNTREEIFY_THRESHOLD = 6;
    static final int MIN_TREEIFY_CAPACITY = 64;
    private static final int MIN_TRANSFER_STRIDE = 16;
    private static int RESIZE_STAMP_BITS = 16;
    private static final int MAX_RESIZERS = (1 << (32 - RESIZE_STAMP_BITS)) - 1;
    private static final int RESIZE_STAMP_SHIFT = 32 - RESIZE_STAMP_BITS;

    /**
     * Encodings for Node hash fields. See above for explanation.
     */
    static final int MOVED     = -1; // hash for forwarding nodes
    static final int TREEBIN   = -2; // hash for roots of trees
    static final int RESERVED  = -3; // hash for transient reservations
    static final int HASH_BITS = 0x7fffffff; // usable bits of normal node hash

    /* ---------------- Fields -------------- */
    transient volatile Node<K,V>[] table;
    private transient volatile Node<K,V>[] nextTable;
    private transient volatile long baseCount;
    private transient volatile int sizeCtl;
    private transient volatile int transferIndex;
    private transient volatile int cellsBusy;
    private transient volatile CounterCell[] counterCells;
}
```

## TreeBin结构

```java
static final class TreeBin<K,V> extends Node<K,V> {
    TreeNode<K,V> root;
    volatile TreeNode<K,V> first;
    volatile Thread waiter;
    volatile int lockState;
    // values for lockState
    static final int WRITER = 1; // set while holding write lock
    static final int WAITER = 2; // set when waiting for write lock
    static final int READER = 4; // increment value for setting read lock
}
```

抛弃了原有的 Segment 分段锁，而采用了 CAS + synchronized 来保证并发安全性。

跟HashMap很像，也把之前的HashEntry改成了Node，但是作用不变，把值和next采用了volatile去修饰，保证了可见性，并且也引入了红黑树，在链表大于一定值的时候会转换（默认是8）。

## put添加元素

1. 根据 key 计算出 hashcode；

2. 判断是否需要进行初始化；

3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功；

4. 如果当前位置的 hashcode == MOVED == -1，则需要进行扩容；

5. 如果都不满足，则利用 synchronized 锁写入数据；

6. 如果数量大于 TREEIFY_THRESHOLD 则要转换为红黑树。

## get查询

1. 根据计算出来的 hashcode 寻址，如果就在桶上那么直接返回值。

2. 如果是红黑树那就按照树的方式获取值。

3. 就不满足那就按照链表的方式遍历获取值。
