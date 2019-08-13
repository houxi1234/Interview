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

   - size > capacity(桶的数量) * loadFactor(装载因子) 

     - 默认情况下 size > 12 (16 * 0.75 = 12)   将进行扩容处理 
     - 例
       - 初始化一个 预计size = 100 的HashMap
       - new HashMap(128)  -->不取2的幂，会默认初始化最靠近100的2的幂
       - 扩容触发 size > 128 * 0.75 = 96
       - 实际在size 大于96之后触发了扩容
       - 所以初始化 new HashMap(256) 合适

   - 扩容过程
     - 红黑树
       - 一个节点标记为红色或黑色

       - 根节点是黑色

       - 如果一个节点是红色，那么它的子节点一定是黑色

       - 红色节点必须有两个黑色子节点

       - 对于每个节点，从该节点到其后代叶节点的简单路径上，均包含相同数目的黑色节点（保证了最长路径不超过最短路径的两倍）

         ![1557028515150](C:\Users\houxi\AppData\Roaming\Typora\typora-user-images\1557028515150.png)

     - 红黑树的插入

       - RBTree为二叉搜索树，按照二叉搜索树的方法进行节点插入
       - RBTree有颜色约束，在插入新节点之后要进行颜色调整
       - 步骤
         - 根节点为NULL，直接插入新节点，并将颜色设置为黑色
         - 根节点不为NULL，找到要插入新节点的位置
         - 插入新节点
         - 判断新插入节点对全树颜色的影响，更新调整颜色

   - 计算```Hash```值

     - 根据存入的key-value对中的，key计算对应的hash值，然后放入相应的桶中。

     - 根据key的hashcode与其hashcode右移16位的异或结果

       - ```java
         static final int hash(Object key) {
                 int h;
                 return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
         }
         ```

     - 方法```hash(Object key)```

       - ```(h = key.hashCode()) ^ (h >>> 16)```
         - 直接取hash值，会丢失高位数据，增大冲突
         - 这种方式又叫扰乱函数，其比直接取hash值冲突改了少10%左右

     - hashcode是一个32位的值，用高16位与低16位进行异或，原因在于求index是是用 （n-1） & hash ，如果hashmap的capcity很小的话，那么对于两个高位不同，低位相同的hashcode，可能最终会装入同一个桶中。那么会造成hash冲突，好的散列函数，应该尽量在计算hash时，把所有的位的信息都用上，这样才能尽可能避免冲突。这就是为什么用高16位与低16位进行异或的原因。

   - capcity 是 2 的幂

     - 计算index时用的是```(n-1) & hash```,这样能保证```n-1```是全为```1```的二进制数，如果不全为```1```的话，存在某一位是```0```，那么```0,1```与```0```的```与```结果都是```0```，这样便有可能将hash不同的两个值最终装到一个桶中，造成冲突。

   - index 计算

     - ```(n-1) & hash``` 而不是 模运算``` hash % n```的好处（在HashTable中依旧是取模运算）
       - 位运算消耗资源更少，更有效率
       - 避免了hashcode为负数(溢出)的情况

   - PUT 操作

     - 流程图![1557045564565](C:\Users\houxi\AppData\Roaming\Typora\typora-user-images\1557045564565.png)

     - ```java
       final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
               Node<K,V>[] tab; Node<K,V> p; int n, i;
           	//判断 table 是否为空，或参数 length == 0 
               if ((tab = table) == null || (n = tab.length) == 0)
                   //初始化 tab 数组 方法--> resize()
                   n = (tab = resize()).length;
           	//判断当前桶是否有元素   计算存放位置 --> i = (n - 1) & hash
               if ((p = tab[i = (n - 1) & hash]) == null)
                   //无元素，初始化当前桶
                   tab[i] = newNode(hash, key, value, null);
               else {
                   //有元素
                   Node<K,V> e; K k;
                   //判断桶中第一个元素的 hash值、key 是否相等
                   if (p.hash == hash &&
                       ((k = p.key) == key || (key != null && key.equals(k))))
                       //相等则覆盖value值
                       e = p;
                   else if (p instanceof TreeNode)
                       //判断是否是红黑树
                       e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
                   else {
                       //链表
                       for (int binCount = 0; ; ++binCount) {
                           //下一个节点是否有元素 --> p.next
                           if ((e = p.next) == null) {
                               //下一个节点无元素，创建一个新的node
                               p.next = newNode(hash, key, value, null);
                               //新增节点之后，需判断链表长度是否 大于 TREEIFY_THRESHOLD = 8
                               //大于则 执行 链表 ---> 红黑树转换 
                               //方法 -->treeifyBin(tab, hash)
                               if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                                   treeifyBin(tab, hash);
                               break;
                           }
                           //下一个节点有元素，判断hash 和 key 是否相同
                           if (e.hash == hash &&
                               ((k = e.key) == key || (key != null && key.equals(k))))
                               break;
                           p = e;
                       }
                   }
                   if (e != null) { // existing mapping for key
                       V oldValue = e.value;
                       if (!onlyIfAbsent || oldValue == null)
                           e.value = value;
                       afterNodeAccess(e);
                       return oldValue;
                   }
               }
               ++modCount;
           	//扩容判断
           	//size > capacity(桶的数量) * loadFactor(装载因子)
           	//size > 16 * 0.75
           	//默认情况下 size > 12 将进行扩容处理
               if (++size > threshold)
                   resize();
               afterNodeInsertion(evict);
               return null;
           }
       ```

   - ```resize()```操作

     - ```java
       final Node<K,V>[] resize() {
           	//扩容前的数组 oldTab
               Node<K,V>[] oldTab = table;
           	//扩容前桶（capacity）的数量 oldCap
               int oldCap = (oldTab == null) ? 0 : oldTab.length;
           	//扩容前的 threshold(临界值) = capacity(桶的数量) * loadFactor(装载因子)
               int oldThr = threshold;
               int newCap, newThr = 0;
               if (oldCap > 0) {
                   //当前元素size 大于最大桶容量
                   if (oldCap >= MAXIMUM_CAPACITY) {
                       //扩容设置为最大值
                       threshold = Integer.MAX_VALUE;
                       //不进行扩容
                       return oldTab;
                   }
                   //扩容前的容量 * 2 小于 最大临界容量 
                   //且 扩容前的容量 大于 默认的初始化容量(16)
                   else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                            oldCap >= DEFAULT_INITIAL_CAPACITY)
                       //扩容为原来的两倍
                       newThr = oldThr << 1; // double threshold
               }
           	//旧容量 oldCap <= 0  oldThr > 0 及尚未初始化
               else if (oldThr > 0) // initial capacity was placed in threshold
                   newCap = oldThr;
           	//旧容量 oldCap <= 0  oldThr <= 0 及尚未初始化
               else {               // zero initial threshold signifies using defaults
                   //capacity 使用默认值 16
                   newCap = DEFAULT_INITIAL_CAPACITY;
                   //threshold(临界值) 使用计算后的默认值 12  = 0.75 * 16
                   newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
               }
           	//oldCap > 0 且 newThr == 0 
               if (newThr == 0) {
                   float ft = (float)newCap * loadFactor;
                   newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY 				? (int)ft : Integer.MAX_VALUE);
               }
               threshold = newThr;
               @SuppressWarnings({"rawtypes","unchecked"})
                   Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
               table = newTab;
               if (oldTab != null) {
                   //遍历旧哈希表的每个桶，将旧的哈希表中的桶复制到新的哈希表中
                   for (int j = 0; j < oldCap; ++j) {
                       Node<K,V> e;
                       if ((e = oldTab[j]) != null) {
                           oldTab[j] = null;
                           //旧桶中只有一个元素node
                           if (e.next == null)
                               //将旧哈希表 的桶 放入 新哈希表 e.hash & (newCap - 1) 位置
                               newTab[e.hash & (newCap - 1)] = e;                
                           else if (e instanceof TreeNode)
                               //桶中结构红黑树
                               ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                           else { // preserve order
                               //链表
                               Node<K,V> loHead = null, loTail = null;
                               Node<K,V> hiHead = null, hiTail = null;
                               Node<K,V> next;
                               do {
                                   next = e.next;
                                   if ((e.hash & oldCap) == 0) {
                                       if (loTail == null)
                                           loHead = e;
                                       else
                                           loTail.next = e;
                                       loTail = e;
                                   }
                                   else {
                                       if (hiTail == null)
                                           hiHead = e;
                                       else
                                           hiTail.next = e;
                                       hiTail = e;
                                   }
                               } while ((e = next) != null);
                               if (loTail != null) {
                                   loTail.next = null;
                                   newTab[j] = loHead;
                               }
                               if (hiTail != null) {
                                   hiTail.next = null;
                                   newTab[j + oldCap] = hiHead;
                               }
                           }
                       }
                   }
               }
               return newTab;
           }
       ```

     - 例： 重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变（因为任何数与0与都依旧是0），是1的话index变成“原索引+oldCap”

     - ![1557111332407](C:\Users\houxi\AppData\Roaming\Typora\typora-user-images\1557111332407.png)

     - 面试![1557112086752](C:\Users\houxi\AppData\Roaming\Typora\typora-user-images\1557112086752.png)

3. 说说你知道的几个Java集合类：list、set、queue、map实现类咯。。。

   - <https://blog.csdn.net/bn493235694/article/details/79600330>

4. 描述一下ArrayList、LinkedList、Vestor各自实现和区别

   - 同步性（线程安全）		
     - ArrayList、LinkedList 不同步   及线程不安全
     - Vestor 同步  线程安全 
   - 数据
     - ArrayList、Vestor 使用Object数组形式存储。 
       - 扩容
     - LinkedList 双向链表
     - 末尾添加，ArrayList、LinkedList 开销一致
     - 列中添加，ArrayList开销大
     - 删除
       - ArrayList中间插入或删除开销大
       - LinkedList固定开销
     - 随机访问
       - ArrayList效率高
       - LinkedList 开销大

5. Java中的队列都有哪些，有什么区别。

6. 反射中，Class.forName和classloader的区别

7. Java7、Java8的新特性(baidu问的,好BT)

8. Java数组和链表两种结构的操作效率，在哪些情况下(从开头开始，从结尾开始，从中间开始)，哪些操作(插入，查找，删除)的效率高

9. Final 修辞的String  

   ```java
   public static void main(String[] args) {
           //常量- 编译期产生
           //变量- 运行期产生
           //常量
           String a1 = "z";
           //常量
           final String a2 = "z";
           //常量
           String b = "za";
           //变量 在编译期间 没进入常量池 运行期间   在堆产生 并返回引用（相当于new 一个 新的值）
           String c1 = a1 + "a";
           //常量 编译期间 计算出值  并在常量池中取值
           String c2 = a2 + "a";
           System.out.println(b == c1);
           System.out.println(b == c2);
       }
   ```

   

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