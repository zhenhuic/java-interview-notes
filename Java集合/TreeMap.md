# TreeMap

- TreeMap实现了NavigableMap接口，而NavigableMap接口继承着SortedMap接口，可以根据key自然排序，致使TreeMap是有序的；
- TreeMap底层是红黑树，它方法的时间复杂度都不会太高：log(n)；
- 非同步，可以使用`Collections.synchronizedSortedMap()`方法，将TreeMap转为同步线程安全的；
- 使用Comparator或者Comparable来比较key是否相等与排序的问题；
- key不能为null，为null为抛出NullPointException的；
- TreeMap只用比较的方式判断两个key是否一样，而不是hash。
- 想要自定义比较，在构造方法中传入Comparator对象，否则使用key的自然排序来进行比较。

## TreeMap结构

```java
private final Comparator<? super K> comparator;
private transient Entry<K,V> root;
private transient int size = 0;
/**
 * The number of structural modifications to the tree.
 */
private transient int modCount = 0;
```

## Entry结构

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left;
    Entry<K,V> right;
    Entry<K,V> parent;
    boolean color = BLACK;
}
```

## put添加元素

```java
public V put(K key, V value);
```

1. 如果红黑树根节点为null，判断key是否为null，判断key是否能利用comparator或排序方法正确地排序，然后创建节点赋给root；

2. 通过比较在红黑树中找到合适的位置：
   - 如果存在与要插入的key相等的节点，直接将该节点的value设置为新的值，返回旧值；
   - 如果就创建新的节点，接入红黑树，调整红黑树，使其平衡。最后将size自增，modCount自增。

## get查询

```java
public V get(Object key) {
    Entry<K,V> p = getEntry(key);
    return (p==null ? null : p.value);
}
```

二叉搜索树，时间复杂度log(n)。

## remove删除元素

```java
public V remove(Object key) {
    Entry<K,V> p = getEntry(key);
    if (p == null)
        return null;

    V oldValue = p.value;
    deleteEntry(p);
    return oldValue;
}
```

`deleteEntry(p);`方法删除节点并且使红黑树平衡。
