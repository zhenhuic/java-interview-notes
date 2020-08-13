# Java 并发编程

1. ThreadLocal 原理，如何使用？内存泄漏的场景？

2. 线程安全的单例模式

- 双重检查

- 静态内部类

- 枚举类

  枚举单例模式能够在序列化和反射中保证实例的唯一性。

3. synchronized 底层实现和锁优化

4. ReentrantLock 的底层实现
5. 线程同步的方式
6. PriorityQueue, DelayQueue, SynchronousQueue


## Synchronized底层实现细节

## 偏向锁

从加锁过程去说，Mark word；

为什么有偏向锁，一般情况下只有一个线程执行；

## 轻量级锁

栈帧中的锁记录，复制Mark word，将Mark word修改为指向锁记录的指针；

轻量级锁是自旋锁，默认自旋10次，线程的挂起和恢复是需要执行系统调用，然后中断进入系统内核态的，这样的过程成本比较高，所以自旋等待，看锁能不能被释放，晚一些去阻塞线程。

适应性自旋锁，自旋的时间不在固定了，而是和前一次同一个锁上的自旋时间以及锁的拥有者的状态来决定。

## AQS

## ReetrantLock

## ReentrantReadWriteLock

## Runnable

## Callable

## ThreadPoolExecutor

## ForkJointPool
