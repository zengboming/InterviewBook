# 集合类

#### 集合框架

```
Collection - List - ArrayList
Collection - List - LinkedList
Collection - List - Vector
Collection - List - Vector - Stack
Collection - Set - HashSet
Collection - Set - TreeSet
Collection - List - LinkedHashSet
Map - HashMap
Map - TreeMap
Map - HashTable
Map - LinkedHashMap
Map - ConcurrentHashMap
```

**Collection**：是最基本的集合接口，一个Collection代表一组Object。

**List**： 是元素存取有序的集合，使用此接口能够精确的控制每个元素插入的位置。List允许有相同的元素。

**Set**： 是元素无序的集合，元素不可重复。

**Map**：Map提供key到value的映射。一个Map中不能包含相同的key，每个key只能映射一个 value。

### List

* #### ArrayList

  默认大小10个元素

* #### LinkedList
* #### LinkedHashSet
* #### Vector
* #### Stack

### Set

* #### HashSet
* #### TreeSet

### Map

* #### HashMap                             **非线程安全  \|  KV允许NULL**

  线程不安全，底层是数组加链表实现的哈希表。允许null作为键和值。HashMap去掉了contains方法。 注意：HashMap不保证元素的迭代顺序。如果需要元素存取有序，请使用LinkedHashMap

  **原理**：维护了一个Entry数组，Entry内部类有key,value，hash和next四个字段，其中next也是一个Entry类型。可以将Entry数组理解为一个个的散列桶。每一个桶实际上是一个单链表。当执行put操作时，会根据key的hashcode定位到相应的桶。遍历单链表检查该key是否已经存在，如果存在，覆盖该value，反之，新建一个新的Entry，并放在单链表的头部。当通过传递key调用get方法时，它再次使用key.hashCode\(\)来找到相应的散列桶，然后使用key.equals\(\)方法找出单链表中正确的Entry，然后返回它的值。

  初始容量，默认大小16个元素。

* #### HashTable                           线程安全      \|  KV不允许NULL

  基于Dictionary类，线程安全，速度快。底层是哈希表数据结构。是同步的。 不允许null作为键和值。

  **原理**：在线程竞争激烈的情况下HashTable的效率非常低下，因为他使用synchronized来保证线程安全，所以当一个线程访问HashTable的同步方法时，其他线程访问HashTable的同步方法时，可能会进入阻塞或轮询状态。如线程1使用put进行添加元素，线程2不但不能使用put方法添加元素，并且也不能使用get方法来获取元素，所以竞争越激烈效率越低。

* #### TreeMap                              **非线程安全  \|  不允许NULL**

  实现Map接口，继承自SortedMap。取出来的是排序后的键值对。

  TreeMap基于红黑树实现。HashMap基于哈希表实现。在需要排序的Map时候才用TreeMap。

* #### ConcurrentHashMap         线程安全      \|  KV不允许NULL

  是JUC包下的一个并发集合。

  **原理**：HashTable容器在竞争激烈的并发环境下表现出效率低下的原因在于所有访问HashTable的线程都必须竞争同一把锁，那假如容器里有多把锁，每一把锁用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效的提高并发访问效率，这就是ConcurrentHashMap所使用的**锁分段技术**，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。

  **结构**：是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入互斥锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素，当对某个HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。

  初始容量，默认大小16个元素。装载因子，默认0.75。并发级别，默认16。

  并发级别（concurrencyLevel）：这个值用来确定Segment的个数，Segment的个数是大于等于concurrencyLevel的第一个2的n次方的数。ConcurrentHashMap依据并发级别把实际 map 划分成若干部分来实现它的可扩展性和线程安全。

* #### LinkedHashMap                非线程安全     \|  KV允许NULL

  类似于HashMap,但是迭代遍历它时，保证迭代的顺序是其**插入的次序**，因为它使用链表维护内部次序。

  此外可以在构造器中设定LinkedHashMap，使之采用LRU算法。使没有被访问过的元素或较少访问的元素出现在前面，访问过的或访问多的出现在后面。这对于需要定期清理元素以节省空间的程序员来说，此功能使得程序员很容易得以实现。

* #### WeakHashMap                  非线程安全     \|  KV允许NULL

  表示弱键映射，WeakHashMap 的工作与正常的 HashMap 类似，但是使用弱引用作为 key，意思就是当 key 对象没有任何引用时，key/value 将会被回收。

  当除了自身有对key的引用外，此key没有其他引用那么此map会自动丢弃此值。

  ```
  Map map = new HashMap();
  Map weakmap = new WeakHashMap();
  map.put(a, "aaa");
  weakmap.put(a, "aaa");
  map.remove(a);
  a = null;
  System.gc();
  //结果：map:b:bbb
  // weakmap:b:bbb
  ```

      当HashMap  remove掉并且将a指向null后，除了weakmap中还保存a外已经没有指向a的指针了，所以weakmap会自动舍弃掉a。



