[toc]

# Java 程序基础

## ==、equals和hashCode()

[面试官爱问的equals与hashCode](https://juejin.im/post/5a4379d4f265da432003874c)
[程序员必须搞清的概念equals和=和hashcode的区别](https://juejin.im/post/584ac23061ff4b0058d5250f)

### 为什么重写 equals 还要重写 hashcode

Object 类默认的 equals 比较规则就是比较两个对象引用的内存地址， 默认的 hashcode 方法是根据对象的内存地址经哈希算法得来的，因此，二个对象 equals 相等，hashcode 一定相等。

hashmap 中通过 hashcode 值来定位存储的索引号，如果处于相同索引位置但存在冲突，则在通过 equal 方法来比较二个对象是否相同。

根据对象的某种性质重写了 equals 方法后，为了保证同一个对象，保证 equals 相同的情况下 hashcode 值必定相同，如果重写了 equals 而未重写 hashcode 方法，可能就会出现两个没有关系的对象 equals 相同的（因为 equal 都是根据对象的特征进行重写的），但 hashcode 确实不相同的。
如果你改写了 equal()方法，令两个实际不是一个对象的两个实例在逻辑上相等了，但是 hashcode 却是不等。所以要记得改写 hashcode。

因为重写的 equal 里一般比较的比较全面比较复杂，这样效率就比较低，而利用 hashCode()进行对比，则只要生成一个 hash 值进行比较就可以了，效率很高，那么 hashCode()既然效率这么高为什么还要 equal()呢？因为 hashCode()并不是完全可靠，有时候不同的对象他们生成的 hashcode 也会一样（生成 hash 值得公式可能存在的问题），此时产生冲突，需要通过 equal 方法解决，所以 hashCode()只能说是大部分时候可靠，并不是绝对可靠，所以我们可以得出：

1. equal()相等的两个对象他们的 hashCode()肯定相等，也就是用 equal()对比是绝对可靠的。
2. hashCode()相等的两个对象他们的 equal()不一定相等，也就是 hashCode()不是绝对可靠的。

### Object 若不重写hashCode()的话，hashCode()如何计算出来的

Object 的 hashcode 方法是本地方法，也就是用 C/C++ 实现的，该方法直接返回对象的内存地址。

### ==比较的是什么

对于对象引用类型:“==”比较的是对象的内存地址。
对于基本类型数据，其实比较的是它的值。

### 若对一个类不重写，它的 equals()方法是如何比较的

如果没有对 equals 方法进行重写，则比较的是引用类型的变量所指向的对象的地址；诸如 String、Date 等类对 equals 方法进行了重写的话，比较的是所指向的对象的内容。

## 一个十进制的数在内存中是怎么存的

二进制补码形式
[原码补码反码介绍](https://www.cnblogs.com/wangsiting/p/7339192.html)

## 为啥有时会出现 4.0-3.6=0.40000001 这种现象

2 进制的小数无法精确的表达 10 进制小数，计算机在计算 10 进制小数的过程中要先转换为 2 进制进行计算，这个过程中出现了误差。

## Java 支持的数据类型有哪些？什么是自动拆装箱

八个基本数据类型：byte，short，int，long，float，double，char，boolean；以及引用类型，引用类型包括类类型、接口类型和数组。整数默认 int 型，小数默认是 double 型，float、long 类型必须加后缀 f、l；

自动装箱和拆箱就是基本类型和其对应引用类型之间的转换，基本类型转换为引用类型后，就可以直接调用包装类中封装好的一些方法

![](https://ws1.sinaimg.cn/large/d4556b75ly1g3mteb1k1kj20l00eidgb.jpg)

## 什么是值传递和引用传递

[参考网址](https://www.cnblogs.com/xiaoxiaoyihan/p/4883770.html)

- 值传递，在方法的调用过程中，实参把它的实际值传递给形参，此传递过程就是将实参的值复制一份传递到函数中，这样如果在函数中对该值（形参的值）进行了操作将不会影响实参的值。
- 引用传递，将对象的地址值传递过去，函数接收的是原始值的首地址值。在方法的执行过程中，形参和实参的内容相同，指向同一块内存地址，也就是说操作的其实都是源数据，所以方法的执行将会影响到实际对象

## 字符串常量池、class常量池和运行时常量池

[字符串常量池、class常量池和运行时常量池](https://blog.csdn.net/qq_26222859/article/details/73135660)

## String 是最基本的数据类型吗

不是，基本数据类型包括：byte,short,int,long,float,double,boolean,char。而 String 是类代表字符串，属于引用类型，所谓引用类型包括：类，接口，数组...

## Java String类为什么是final的

1. 为了实现字符串池

2. 为了线程安全

3. 为了实现String可以创建HashCode不可变性

[在java中String类为什么要设计成final？](https://www.zhihu.com/question/31345592)

## int 和 Integer 有什么区别

[参考网址](http://www.cnblogs.com/liuling/archive/2013/05/05/intAndInteger.html)

Ingeter 是 int 的包装类，int 的初值为 0，Ingeter 的初值为 null。java 可以通过自动拆箱和装箱对 int 和 Integer 进行转化。

## String、StringBuffer、StringBuffer

三者区别：

1. String 为字符串常量，每次对 string 操作都会产生一个新的对象，而 StringBuilder 和 StringBuffer 均为字符串变量，即 String 对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。因此在运行速度快慢为：StringBuilder > StringBuffer > String
2. 在线程安全上，StringBuilder 是线程不安全的，而 StringBuffer 是线程安全的
3. String 适用于少量的字符串操作的情况；StringBuilder 适用于单线程下在字符缓冲区进行大量操作的情况；StringBuffer 适用多线程下在字符缓冲区进行大量操作的情况

以下情况走 StringBuilder：

[String 字符串拼接问题，到底什么时候会走 StringBuilder？](https://www.jianshu.com/p/a80c9b2b89cd)

1. 通过变量和字符串拼接，java 是需要先到内存找变量对应的值，才能进行完成字符串拼接的工作，这种方式 java 编译器没法优化，只能走 StringBuilder 进行拼接字符串，然后调用 toString 方法。当然返回的结果和常量池中的字符串的内存地址是不一样的。
2. 直接在表达式里写值，java 不用根据变量去内存里找对应的值，可以在编译的时候直接对这个表达式进行优化，不用走 StringBilder，优化后的表达式直接指向常量池的字符串

比较：

[StringBuffer和StringBuilder源码分析](https://yq.aliyun.com/articles/629416)

继承层次相同，都继承了 AbstractStringBuilder ，实现了 Serializable 和 CharSequence 接口;

成员变量：

```java
char[] value
private transient char[] toStringCache
```

底层都是用字符数组 `char[]` 实现，存储字符串，默认的大小为 16。String 的 value 数组使用 final 修饰，不能变动，StringBuffer 和StringBuilder 的 value 数组没有 final 修饰，是可变的。  关于数组的大小，默认的初始化容量是 16，假如初始化的时候，传入字符串，则最终的容量将是 (传入字符串的长度 + 16) 。 

`toStringCache` 是 StringBuffer 特有，缓存 `toString` 最后一次返回的值。 多次连续调用 `toString` 方法的时候由于这个字段的缓存就可以少了 `Arrays.copyOfRange` 的操作

```java
public synchronized String toString() {
    if (toStringCache == null) {  // toStringCache为空，第一次操作
        toStringCache = Arrays.copyOfRange(value, 0, count);
    }
    // 使用缓存的toStringCache，实际只传递了引用，没有复制操作
    return new String(toStringCache, true);
}
```

两者方法最大的区别是：StringBuffer 是线程安全的，StringBuilder 是非线程安全的。实现是StringBuffer 在和 StringBuilder 相同的方法上加了 `synchronized` 修饰。

StringBuffer的`append()` 方法：

```java
public synchronized StringBuffer append(String str) {
    toStringCache = null;
    super.append(str);
    return this;
}
```

底层存储的扩容机制：

初始容量都是 16，扩容机制都是"以前容量 * 2 + 2" 的形式，如果扩容之后仍不满足所需容量，则直接扩容到所需容量。

## 我们在 web 应用开发过程中经常遇到输出某种编码的字符，如 iso8859-1 等，如何输出一个某种编码的字符串

通过 new 一个字符串对象，把原始编码和需要输出编码类型传进构造器中

```java
public String translate (String str) {
    String tempStr = "";
    try {
        tempStr = new String(str.getBytes("ISO-8859-1"), "GBK");
        tempStr = tempStr.trim();
    } catch  (Exception e)  {
        System.err.println(e.getMessage());
    }
    return tempStr;
}
```

## 在 Java 中，如何跳出当前的多重嵌套循环

在 Java 中，要想跳出多重循环，可以在外面的循环语句前定义一个标号，
然后在里层循环体的代码中使用带有标号的 break 语句，即可跳出外层循环


## 方法变长参数

可变参数在被使用的时候，他首先会创建一个数组，数组的长度就是调用该方法时传递的实参的个数，然后再把参数值全部放到这个数组当中，然后再把这个数组作为参数传递到被调用的方法中

# 面向对象

## 三大特性

封装、继承、多态

## Object 类的方法

1. getClass()
2. hashCode()
3. equals(Object obj)
4. clone()
5. toString()
6. notify()
7. notifyAll()
8. wait()
9. wait(long timeoutMillis)
10. wait(long timeoutMillis, int nanos)
11. finalize()

## 重载(Overload)和重写(Override)的区别？相同参数不同返回值能重载吗？Overload 的方法是否可以改变返回值的类型

- 相同参数不同返回值不可以重载，重载的方法不能根据返回类型进行区分。
- 重写：一个类是继承一个父类时，从父类那儿继承来的方法，进行重新写过。
- 重载：多个同名方法，名称相同，但是参数不同，可以通过参数来识别调用的是哪一个方法。

## final、static、this、super 关键字

[final,static,this,super 关键字总结](https://gitee.com/SnailClimb/JavaGuide/blob/master/docs/java/basic/final,static,this,super.md)

## 一个类不能被继承，除了 final 关键字之外，还有什么方法（从构造函数考虑）

构造方法只能重载，不能被重写。

## final 关键字

1. 修饰类：表示该类不能被继承；
2. 修饰方法：表示方法不能被重写；
3. 修饰变量：表示变量只能赋值一次且赋值以后值不能被修改（常量）。

## Java 中是否可以覆盖(override)一个 private 或者是 static 的方法

静态，就是禁止多态。所以不能重写 static 方法。 private 是私有的，肯定不能重写了。

## 静态变量和字符串常量存在哪

方法区，静态变量是共享内容，栈是不共享的，堆存放 new 关键字创建的对象实例，方法区是共享的区域，它用于存储已被虚拟机加载的类信息，常量，静态变量，即时编译后的代码等数据。

JDK 1.8之前方法区是由永久代实现，1.8之后方法区被拿到元空间，数据直接内存，所以静态变量和常量池也一起在元空间。而字符串常量原本在方法区的，1.8之后被拿出来单独放在堆空间中。

## 解释 extends 和 super 泛型限定符-上界不存下界不取

extends 上限通配符，用来限制类型的上限，只能传入本类和子类，add 方法受阻，可以从一个数据类型里获取数据；

```java
List<? extends Number> eList = null;
eList = new ArrayList<Integer>();
eList = new ArrayList<Long>();
```

super 下限通配符，用来限制类型的下限，只能传入本类和父类，get 方法受阻，可以把对象写入一个数据结构里；

```java
List<? super Integer> sList = null;
sList = new ArrayList<Number>();
```

## Java 支持多继承么

java 只支持单继承，这是由于安全性的考虑，如果子类继承的多个父类里面有相同的方法或者属性，子类将不知道具体要继承哪个，而接口可以多实现，是因为接口只定义方法，而没有具体的逻辑实现，多实现也要重新实现方法。

## 接口的默认方法


## 接口和抽象类的区别

1. 接口的方法默认是 public，所有方法在接口中不能有实现，抽象类可以有非抽象的方法， 可以由 public protected 或者默认修饰。 java1.8 以前接口中方法不能有方法体，1.8 以后可以由 default 关键字修 饰，从而可以拥有方法体。
2. 接口中的实例变量默认是 final 类型的，而抽象类中则不一定
3. 一个类可以实现多个接口，但最多只能实现一个抽象类
4. 一个类实现接口的话要实现接口的所有方法，而抽象类不一定

## 枚举

当我们使用 enmu 来定义一个枚举类型的时候，编译器会自动帮我们创建一个 final 类型的类继承 Enum 类，所以枚举类型不能被继承

[Java 枚举(enum) 详解7种常见的用法](https://blog.csdn.net/qq_27093465/article/details/52180865)

## 内部类

内部类之所以也是语法糖，是因为它仅仅是一个编译时的概念，outer.java 里面定义了一个内部类 inner，一旦编译成功，就会生成两个完全不同的 .class 文件了，分别是 outer.class 和 outer\$inner.class。所以内部类的名字完全可以和它的外部类名字相同。

# Java 异常体系

## Java 异常的分类与处理方式

[Java提高篇——Java 异常处理](https://www.cnblogs.com/Qian123/p/5715402.html)

[异常的概念和Java异常体系结构](https://blog.csdn.net/liuhenghui5201/article/details/18675391)

[揭晓Java异常体系中的秘密](https://juejin.im/post/5aa64da06fb9a028d4443b61)

## 常见的异常

[Java常见异常总结](https://www.jianshu.com/p/a03c8807bbbc)

[Java ConcurrentModificationException异常原因和解决方法](https://www.cnblogs.com/dolphin0520/p/3933551.html)

# 泛型

## 讲讲什么是泛型

[深入理解Java泛型](https://juejin.im/post/5b614848e51d45355d51f792)

泛型是一种参数化类型，它的<>里面可以放任何类型，而且不要强转，它是多态的一种体现。 泛型多用于容器中，往容器中方数据，事先约定什么类型数据，放的时候会检查，不是正确的类型放入时会报错，这样可以建立安全的数据，也避免了强制类型转换。

泛型也是 Java 提供的语法糖,只不过是将类型检查从运行期提到编译器.运行时都会被擦除为 Object.,运行的时候都会在方法的入口和出口进行转换(就是发生擦除的边界位置)。

1. 泛型以及泛型擦除
2. List<>类型的list，可以加入无继承关系的B类型对象吗？如何加入？

对于 Java 虚拟机来说，他根本不认识 `Map<String, String> map` 这样的语法。需要在编译阶段通过类型擦除的方式进行解语法糖。

# 反射机制

## 反射原理以及使用场景

[Java 反射由浅入深 | 进阶必备](https://juejin.im/post/598ea9116fb9a03c335a99a4)

[大白话说Java反射：入门、使用、原理](https://www.cnblogs.com/chanshuyi/p/head_first_of_reflection.html)

[在Java的反射中，Class.forName和ClassLoader的区别](https://zhuanlan.zhihu.com/p/68498855)

## 如何通过反射创建对象

[参考网址](https://www.cnblogs.com/qjlbky/p/5929452.html)

1. 通过默认的构造器通过 Class 的 newInstance()方法来获取
2. 通过指定的构造器来创建

```java
Class clazz = Class.forName(className);

//1.通过Class获取指定构造方法，比如带两个参数
Constructor cons =clazz.getConstructor(String.class, int.class);  //拿的是公有的

//2.通过指定的构造器对象进行对象的初始化。
Object obj = cons.newInstance("lisisi",23);
```

# 注解

[JAVA 注解的基本原理](https://juejin.im/post/5b45bd715188251b3a1db54f)

# I/O



# 序列化

## 序列化和反序列化

[java序列化，看这篇就够了](https://juejin.im/post/5ce3cdc8e51d45777b1a3cdf)

## 反序列化失败的场景

虚拟机是否允许反序列化，不仅取决于类路径和功能代码是否一致，一个非常重要的一点是两个类的序列化 ID 是否一致（就是 private static final long serialVersionUID = 1L）。

# 正则表达式

1. 概念：
   在编写处理字符串的程序时，经常会有查找符合某些复杂规则的字符串的需要。正则表达式就是用于描述这些规则的工具。换句话说，正则表达式就是记录文本规则的代码。
2. java 与正则相关的工具主要在 java.util.regex 包中；此包中主要有两个类：Pattern、Matcher

- Pattern：
  Pattern 类表示正则表达式对象，用于创建一个正则表达式,也可以说创建一个匹配模式,它的构造方法是私有的,不可以直接创建,但可以通过 Pattern.complie(String regex)简单工厂方法创建一个正则表达式
  pattern() 方法返回正则表达式的字符串形式,其实就是返回 Pattern.complile(String regex)的 regex 参数

```java
Pattern p=Pattern.compile("\\w+");
p.pattern();//返回 \w+
```

- Matcher：Matcher 对象是对输入字符串进行解释和匹配操作的引擎与 Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。

```java
// 创建 Pattern 对象
        Pattern r = Pattern.compile(pattern);
        // 现在创建 matcher 对象
        Matcher m = r.matcher(line);
        if (m.find()) {
            System.out.println("Found value: " + m.group(0));
            System.out.println("Found value: " + m.group(1));
        } else {
            System.out.println("NO MATCH");
        }
```

# Java8新特性

1. Lambda 表达式 − Lambda 允许把函数作为一个方法的参数（函数作为参数传递进方法中）
2. 接口的默认方法和静态方法 − 可以在接口中定义默认方法，使用 default 关键字，并提供默认的实现。所有实现这个接口的类都会接受默认方法的实现，除非子类提供的自己的实现
3. 方法引用 − 方法引用使得开发者可以直接引用现存的方法、Java 类的构造方法或者实例对象。可以与 lambda 联合使用
4. 重复注解- 注解有一个很大的限制是：在同一个地方不能多次使用同一个注解。Java 8 打破了这个限制，引入了重复注解的概念，允许在同一个地方多次使用同一个注解
   ...... 等等（重点 lambda 表达式）

## 说说 Lamda 表达式的优缺点

- 优点：1. 简洁; 2. 非常容易并行计算; 3. 可能代表未来的编程趋势
- 缺点：1. 若不用并行计算，很多时候计算速度没有比传统的 for 循环快。（并行计算有时需要预热才显示出效率优势）2. 不容易调试。3. 若其他程序员没有学过 lambda 表达式，代码不容易让其他语言的程序员看懂。