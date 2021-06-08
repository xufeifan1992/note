## HashMap和HashTable区别



* HashMap方法没有synchronized关键字修饰，线程不安全，HashTable线程安全
* HashMap允许Key和Value为null，而HashTable不允许



### 底层实现



* 数组+链表
* jdk8开始链表的长度到8，数组长度超过64链表转为红黑树，元素以内部类Node节点存在
  * 计算Key的hash值，二次hash然后对数组长度取模，对应到数组下标
  * 如果没有产生hash冲突(下标位置没有元素),则直接创建Node存入数组
  * 如果产生hash冲突，先进行equals比较，相同则取代该元素，不同则判断链表长度插入链表，链表长度达到8并且数组长度到达64则转化为红黑树，长度低于6则将红黑树转为链表
  * key为null时，存储在下标为0de位置



### ConcurrentHashMap原理

​    

**jdk7和jdk8的区别**

* hashTable才用synchronized锁
* ConcurrentHashMap才用分段锁机制，效率更高

​    

----

### DK1.7版本的CurrentHashMap的实现原理

在JDK1.7中ConcurrentHashMap采用了**数组+Segment+分段锁**的方式实现。

**1.Segment(分段锁)**

ConcurrentHashMap中的**分段锁称为Segment**，它即类似于HashMap的结构，即内部拥有一个Entry数组，数组中的每个元素又是一个链表,同时又是一个ReentrantLock（Segment继承了ReentrantLock）。

**2.内部结构**

ConcurrentHashMap使用分段锁技术，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。



**坏处**

这一种结构的带来的副作用是Hash的过程要比普通的HashMap要长

**好处**

写操作的时候可以只对元素所在的Segment进行加锁即可，不会影响到其他的Segment，这样，在最理想的情况下，ConcurrentHashMap可以最高同时支持Segment数量大小的写操作（刚好这些写操作都非常平均地分布在所有的Segment上）。

所以，通过这一种结构，ConcurrentHashMap的并发能力可以大大的提高。

  

-----

### JDK1.8版本的CurrentHashMap的实现原理

JDK8中ConcurrentHashMap参考了JDK8 HashMap的实现，采用了**数组+链表+红黑树**的实现方式来设计，**内部大量采用CAS(乐观锁)操作，这里我简要介绍下CAS。**

CAS是compare and  swap的缩写，即我们所说的比较交换。cas是一种基于锁的操作，而且是乐观锁。在java中锁分为乐观锁和悲观锁。悲观锁是将资源锁住，等一个之前获得锁的线程释放锁之后，下一个线程才可以访问。而乐观锁采取了一种宽泛的态度，通过某种方式不加锁来处理资源，比如通过给记录加version来获取数据，性能较悲观锁有很大的提高。

CAS 操作包含三个操作数 ——  内存位置（V）、预期原值（A）和新值(B)。如果内存地址里面的值和A的值是一样的，那么就将内存里面的值更新成B。CAS是通过无限循环来获取数据的，若果在第一轮循环中，a线程获取地址里面的值被b线程修改了，那么a线程需要自旋，到下次循环才有可能机会执行。

**JDK8中彻底放弃了Segment转而采用的是Node，其设计思想也不再是JDK1.7中的分段锁思想。**

**Node：保存key，value及key的hash值的数据结构。其中value和next都用volatile修饰，保证并发的可见性**



```java
<strong>class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    volatile V val;
    volatile Node<K,V> next;
    //... 省略部分代码
} </strong>
```



**Java8 ConcurrentHashMap结构基本上和Java8的HashMap一样，不过保证线程安全性。**

在JDK8中ConcurrentHashMap的结构，由于引入了红黑树，使得ConcurrentHashMap的实现非常复杂，我们都知道，红黑树是一种性能非常好的二叉查找树，其查找性能为O（logN），但是其实现过程也非常复杂，而且可读性也非常差，Doug
 Lea的思维能力确实不是一般人能比的，早期完全采用链表结构时Map的查找时间复杂度为O（N），JDK8中ConcurrentHashMap在链表的长度大于某个阈值的时候会将链表转换成红黑树进一步提高其查找性能。

  

-----

## 总结

**1.数据结构**：取消了Segment分段锁的数据结构，取而代之的是数组+链表+红黑树的结构。
 **2.保证线程安全机制**：JDK1.7采用segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。
 **3.锁的粒度**：原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁（Node）。
 **4.链表转化为红黑树**:定位结点的hash算法简化会带来弊端,Hash冲突加剧,因此在链表节点数量大于8时，会将链表转化为红黑树进行存储。
 **5.查询时间复杂度**：从原来的遍历链表O(n)，变成遍历红黑树O(logN)。