[toc]

# LinkedHashMap

## LinkedHashMap结构

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V> {
    transient LinkedHashMap.Entry<K,V> head;
    transient LinkedHashMap.Entry<K,V> tail;
    /**
     * The iteration ordering method for this linked hash map:
     * <tt>true</tt> for access-order, <tt>false</tt> for insertion-order.
     */
    final boolean accessOrder;
}
```

## Entry结构

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;

    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

## get查询

```java
public V get(Object key) {
    Node<K,V> e;
    if ((e = getNode(hash(key), key)) == null)
        return null;
    if (accessOrder)
        afterNodeAccess(e);
    return e.value;
}
```

LikedHashMap的get()方法底层是调用的HashMap的getNode()方法，不同的是如果`accessOrder`为true（access-order iteration），会执行`afterNodeAccess(e);`操作。

## afterNodeAccess方法实现

```java
void afterNodeAccess(Node<K,V> e);
```

LinkedHashMap重写了HashMap中afterNodeAccess这个空方法，将节点e移动到链表的尾部。

这里注意遍历LinkedHashMap的时候，iterator是从head节点开始遍历的，所以如果是设置了access-order，靠近尾节点的是最近使用到的，而靠近头节点的（先被遍历到）是最近最少使用到的。

## put添加元素

LinkedHashMap没有对HashMap的put()方法进行重写，直接使用HashMap的put()方法添加元素。

如果是哈希表中已经存在了key相等的节点，那么修改value后执行上述的afterNodeAccess方法；如果原来不存在相同的key，那么执行重写的`newNode()`方法，最后如果需要扩容，扩容结束后执行`afterNodeInsertion(evict);`。

## 重写的newNode方法

```java
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
    LinkedHashMap.Entry<K,V> p = new LinkedHashMap.Entry<K,V>(hash, key, value, e);
    linkNodeLast(p); // link at the end of list
    return p;
}
```

这里跟HashMap的区别就是创建了一个LinkedHashMap.Entry，并将该节点链接到链表的尾部。

## 用来实现LRU的afterNodeInsertion方法

```java
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}

protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
    return false;
}
```

afterNodeInsertion方法在put方法返回之前被回调，该方法在HashMap中是个空方法，就是为了给LinkedHashMap重写添加功能用的。

这个方法的逻辑是，如果需要删除且`removeEldestEntry(first)`返回true，那么就删除头节点，也就是最早添加的节点（如果是access-order，就是最近最少使用的节点）。

而removeEldestEntry方法默认实现就是返回false，所以默认是不会删除节点的。只有我们自己需要以基础LinkedHashMap的方式实现LRU Cache时需要重写removeEldestEntry方法。

## 利用LinkedHashMap实现LRU Cache

```java
public class LRUCache extends LinkedHashMap<Integer, Integer> {
    private int capacity;

    public LRUCache_146(int capacity) {
        // accessOrder 为 true，默认是 insertion-order
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        super.put(key, value);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity;
    }
}
```
