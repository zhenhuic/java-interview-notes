# PriorityQueue

## PriorityQueue结构

```java
public class PriorityQueue<E> extends AbstractQueue<E> implements java.io.Serializable {
    private static final int DEFAULT_INITIAL_CAPACITY = 11;
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    transient Object[] queue; // non-private to simplify nested class access
    private int size = 0;
    private final Comparator<? super E> comparator;
    transient int modCount = 0; // non-private to simplify nested class access
```

## 执行构造方法时初始化

```java
public PriorityQueue(int initialCapacity, Comparator<? super E> comparator) {
    if (initialCapacity < 1)
        throw new IllegalArgumentException();
    this.queue = new Object[initialCapacity];
    this.comparator = comparator;
}
```

其他重载的构造方法也都是调用这个构造方法，初始化一个大小为initialCapacity的Object数组，同时将comparator赋值。

## add添加元素

```java
public boolean add(E e) {
    return offer(e);
}
public boolean offer(E e);
```

1. 如果优先队列的size大于或等于队列数组长度，就执行扩容。

2. 向数组中添加元素：

   - 如果size为0，那么直接放在第一个位置。
   - 如果size不为0，那么执行`siftUp(i, e);`，将元素向上提升，保持堆的性质（默认小顶堆）。

## grow队列扩容

```java
private void grow(int minCapacity) {
    int oldCapacity = queue.length;
    // Double size if small; else grow by 50%
    int newCapacity = oldCapacity + ((oldCapacity < 64) ? (oldCapacity + 2) : (oldCapacity >> 1));
    // overflow-conscious code
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    queue = Arrays.copyOf(queue, newCapacity);
}
```

PriorityQueue的默认初始化容量是11，执行扩容时，如果原来数组的容量小于64，那么扩容到原来的2倍加2；如果原来容量大于等于64，那么扩容到原来的1.5倍。

再将原来数组的数据复制到一个新的容量的数组中。

## 将堆中的某个元素向上提升

```java
private void siftUp(int k, E x) {
    if (comparator != null)
        siftUpUsingComparator(k, x);
    else
        siftUpComparable(k, x);
}
```

PriorityQueue默认是小顶堆的结构，该方法将索引为k位置的元素向上提升，直到该元素比它的父节点大，以维持堆的结构。

## poll取出堆顶元素

```java
public E poll() {
    if (size == 0)
        return null;
    int s = --size;
    modCount++;
    E result = (E) queue[0];
    E x = (E) queue[s];
    queue[s] = null;
    if (s != 0)
        siftDown(0, x);
    return result;
}
```

取出堆顶元素，将数组中最后一个元素放入数组第一个位置，并将该元素执行向下降低。
