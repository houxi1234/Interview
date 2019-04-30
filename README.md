# Interview
**一、Java基础**

1. String类为什么是final的。

   - 理解```final```

     - 修辞类，类不能被继承
     - 修辞方法，方法不能被重写
     - 修辞类变量，变量必须初始化

   - 字符串池```String Pool```

     - 字符串创建方式

       - ```java
         String str = "interview";
         //采用字面值的方式创建：JVM首先会去字符串池中查找是否存在"interview"这个对象。如果不存在，则在字符串池中创建这个对象。如果存在，则直接将池中的"interview"的对象地址返回，赋值给str。
         ```

       - ```java
         String str = new String("interview");
         //采用 new 关键字创建字符串对象，JVM首先去字符串池中查询是否有"interview"这个字符串对象。
         //如果没有，则首先在字符串池中创建这个对象，再在堆中创建一个"interview"对象，然后将堆中的这个对象赋值给 str 。
         //如果有，则直接在堆中创建一个"interview"对象，然后将堆中的这个对象赋值给 str 。
         ```

     - 由字符串创建方式可知，```String``` 对象必须是不可变得。这样才能**保证多个引用同时指向字符串池中的同一对象**，保证**多线程安全**。倘若字符串是可变的，那么就会出现，一个引用改变了对象的值，会造成其他引用的值也随着改变。

     - 优点

       - 避免相同内容的字符串创建
         - 节省内存空间
         - 节省创建时间

     - 缺点

       - 牺牲了JVM常量池遍历对象的时间

   - 总结

     - 实现常量池，提高字符串创建效率，节约内存空间
     - 安全性保证，防止重写```String```的方法去直接调用操作系统API

2. HashMap的源码，实现原理，底层结构。

   ##### 	底层结构

   - 数据结构

     - JDK 8 之前： 数组+链表
     - JDK 8 之后： 数组+链表+红黑树

   - 桶：数组中的每一个元素称为桶

     - 名称：segment

   - bin

     - 桶中连的链表或者红黑树中的每一个元素成为bin

   - 桶的数量

     - 名称：capacity
     - 大小
       - 默认16
       - 初始化： 容量为2的冥（若初始化的容量非2的冥，则去传入值最近的2的整数冥）
       - 为什么设计为2的冥：

   - 装载因子

     - loadFactor

     - 作用

       - 衡量HashMap满的程度

     - 默认值

       - 0.75f
       - 为什么是0.75f
         - 负载因子0.75是对空间和时间效率的一个平衡选择，建议大家不要修改，除非在时间和空间比较特殊的情况下，如果内存空间很多而又对时间效率要求很高，可以降低负载因子Load factor的值；相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子loadFactor的值，这个值可以大于1。

       - size/capacity

   - 树化阈值

     - **TREEIFY_THRESHOLD** = 8
     - 当单个segment长度超过8之后，链表将转换为红黑树

   - 链表化阈值

     - **UNTREEIFY_THRESHOLD**  = 6
     - resize后或者删除操作后单个segment的容量低于阈值（6）时，将红黑树转化为链表

   - 最小树化容量

     - **MIN_TREEIFY_CAPACITY**  =  64
     - 当桶中的bin被树化时最小的hash表容量，低于该容量时不会树化。

   ##### 扩容

   - 单个量表长度超过8时
     - 若map中存放的所有元素（所有bin），小于最小树化容量（64），则不会转换为红黑树
     - 若map中存放的所有元素（所有bin），大于等于最小树化容量（64），则转换为红黑树
   - 扩容过程
     - 红黑树
       - 一个节点标记为红色或黑色
       - 根节点是黑色
       - 如果一个节点是红色，那么它的子节点一定是黑色
       - 红色节点必须有两个黑色子节点
       - 

3. 说说你知道的几个Java集合类：list、set、queue、map实现类咯。。。

4. 描述一下ArrayList和LinkedList各自实现和区别

5. Java中的队列都有哪些，有什么区别。

6. 反射中，Class.forName和classloader的区别

7. Java7、Java8的新特性(baidu问的,好BT)

8. Java数组和链表两种结构的操作效率，在哪些情况下(从开头开始，从结尾开始，从中间开始)，哪些操作(插入，查找，删除)的效率高

**二、Java IO**

> 1. 讲讲IO里面的常见类，字节流、字符流、接口、实现类、方法阻塞。
>
> 2. 讲讲NIO。
>
> 3. String 编码UTF-8 和GBK的区别?
>
> 4. 什么时候使用字节流、什么时候使用字符流?
>
> 5. 递归读取文件夹下的文件，代码怎么实现

**三、JVM**

1. Java的内存模型以及GC算法

2. jvm性能调优都做了什么

3. 介绍JVM中7个区域，然后把每个区域可能造成内存的溢出的情况说明

4. 介绍GC 和GC Root不正常引用。

5. 自己从classload 加载方式，加载机制说开去，从程序运行时数据区，讲到内存分配，讲到String常量池，讲到JVM垃圾回收机制，算法，hotspot。反正就是各种扩展

6. jvm 如何分配直接内存， new 对象如何不分配在堆而是栈上，常量池解析

7. 数组多大放在 JVM 老年代（不只是设置 PretenureSizeThreshold ，问通常多大，没做过一问便知）

8. 老年代中数组的访问方式

9. GC 算法，永久代对象如何 GC ， GC 有环怎么处理

10. 谁会被 GC ，什么时候 GC

11. 如果想不被 GC 怎么办

12. 如果想在 GC 中生存 1 次怎么办

**四、开源框架**

1. hibernate和ibatis的区别

2. 讲讲mybatis的连接池。

3. spring框架中需要引用哪些jar包，以及这些jar包的用途

4. springMVC的原理

5. springMVC注解的意思

6. spring中beanFactory和ApplicationContext的联系和区别

7. spring注入的几种方式（循环注入）

8. spring如何实现事物管理的

9. springIOC

10. spring AOP的原理

11. hibernate中的1级和2级缓存的使用方式以及区别原理（Lazy-Load的理解）

12. Hibernate的原理体系架构，五大核心接口，Hibernate对象的三种状态转换，事务管理。

**五、多线程**

1. Java创建线程之后，直接调用start()方法和run()的区别

2. 常用的线程池模式以及不同线程池的使用场景

3. newFixedThreadPool此种线程池如果线程数达到最大值后会怎么办，底层原理。

4. 多线程之间通信的同步问题，synchronized锁的是对象，衍伸出和synchronized相关很多的具体问题，例如同一个类不同方法都有synchronized锁，一个对象是否可以同时访问。或者一个类的static构造方法加上synchronized之后的锁的影响。

5. 了解可重入锁的含义，以及ReentrantLock 和synchronized的区别

6. 同步的数据结构，例如concurrentHashMap的源码理解以及内部实现原理，为什么他是同步的且效率高

7. atomicinteger和Volatile等线程安全操作的关键字的理解和使用

8. 线程间通信，wait和notify

9. 定时线程的使用

10. 场景：在一个主线程中，要求有大量(很多很多)子线程执行完之后，主线程才执行完成。多种方式，考虑效率。

11. 进程和线程的区别

12. 什么叫线程安全？举例说明

13. 线程的几种状态

14. 并发、同步的接口或方法

15. HashMap 是否线程安全，为何不安全。 ConcurrentHashMap，线程安全，为何安全。底层实现是怎么样的。

16. J.U.C下的常见类的使用。 ThreadPool的深入考察； BlockingQueue的使用。（take，poll的区别，put，offer的区别）；原子类的实现。

17. 简单介绍下多线程的情况，从建立一个线程开始。然后怎么控制同步过程，多线程常用的方法和结构

18. volatile的理解

**六、并发与性能调优**

1. 有个每秒钟5k个请求，查询手机号所属地的笔试题(记得不完整，没列出)，如何设计算法?请求再多，比如5w，如何设计整个系统?

2. 高并发情况下，我们系统是如何支撑大量的请求的

3. 集群如何同步会话状态

4. 负载均衡的原理
5. 5如果有一个特别大的访问量，到数据库上，怎么做优化（DB设计，DBIO，SQL优化，Java优化）

6. 如果出现大面积并发，在不增加服务器的基础上，如何解决服务器响应不及时问题“。

7. 假如你的项目出现性能瓶颈了，你觉得可能会是哪些方面，怎么解决问题。

8. 如何查找 造成 性能瓶颈出现的位置，是哪个位置照成性能瓶颈。

9. 你的项目中使用过缓存机制吗？有没用用户非本地缓存

![1556349462423](C:\Users\houxi\AppData\Roaming\Typora\typora-user-images\1556349462423.png)