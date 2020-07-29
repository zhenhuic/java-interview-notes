# MySQL

怎样会造成死锁

## 慎用外键

1. 外键的优点

    外键约束使得程序员更不容易将不一致性引入数据库，而且设计合适外键有助于以文档方式记录表间关系。

2. 外键的缺点

    但这些优点是以服务器为执行必要的检查而花费额外的开销为代价的。服务器进行额外的检查会影响性能。

    其次外键对并发性能的影响很大，因每次修改数据都需要去另外一个表检查数据，需要获取额外的锁（以确保事务完成之前，父表的记录不会被删除）高并发环境下出现性能问题，更好的办法是**在应用层实现外键约束**。

[MySQL外键字段必须索引](https://blog.csdn.net/sweeper_freedoman/article/details/61426736)

## drop，delete与truncate的区别

[drop,delete与truncate的区别](https://www.jianshu.com/p/9d6c6e5d676f)

## 加载MySQL驱动类为什么用Class.forName()方法
