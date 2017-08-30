#### ConcurrentHashMap使用场景

1. ConcurrentHashMap的应用场景是高并发，但是并不能保证线程安全，而同步的HashMap和HashMap的是锁住整个容器，而加锁之后ConcurrentHashMap不需要锁住整个容器，只需要锁住对应的Segment就好了，所以可以保证高并发同步访问，提升了效率。
2. 可以多线程写。 ConcurrentHashMap把HashMap分成若干个Segmenet
   1. get时，不加锁，先定位到segment然后在找到头结点进行读取操作。而value是volatile变量，所以可以保证在竞争条件时保证读取最新的值，如果读到的value是null，则可能正在修改，那么就调用ReadValueUnderLock函数，加锁保证读到的数据是正确的。
   2. Put时会加锁，一律添加到hash链的头部。
   3. Remove时也会加锁，由于next是final类型不可改变，所以必须把删除的节点之前的节点都复制一遍。
   4. ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术。它使用了多个锁来控制对Hash表的不同Segment进行的修改。

#### JDK1.8以前

看看源码就知道ConcurrentHashMap使用了分段锁，默认的并发度是16。ConcurrentHashMap的并发度就是segment的大小，默认为16，这意味着最多同时可以有16条线程操作ConcurrentHashMap，这也是ConcurrentHashMap对Hashtable的最大优势。

ConcurrentHashMap是由Segment数组结构和HashEntry数组结构组成。Segment是一种可重入锁ReentrantLock，在ConcurrentHashMap里扮演锁的角色，HashEntry则用于存储键值对数据。一个ConcurrentHashMap里包含一个Segment数组，Segment的结构和HashMap类似，是一种数组和链表结构， 一个Segment里包含一个HashEntry数组，每个HashEntry是一个链表结构的元素， 每个Segment守护者一个HashEntry数组里的元素,当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。每个Segment中有一个Entry数组，Entry中成员value是volatile修饰，其他成员通过final修饰。get操作不用加锁，put和remove操作需要加锁，因为value通过volatile保证可见性。

#### 源码解析

 ConcurrentHashMap中主要实体类就是三个：ConcurrentHashMap（整个Hash表）,Segment（桶），HashEntry（节点）。

```
 static final class HashEntry<K,V> {  
     final K key;  
     final int hash;  
     volatile V value;  
     final HashEntry<K,V> next;  
 } 
```

put头插法，因为next指针为final的，不可修改。

remove，如果删除中间结点，则需要将要删除节点的前面所有节点整个复制一遍，最后一个节点指向要删除结点的下一个结点。

get不加锁，value为volatile类型。

#### JDK1.8

它摒弃了Segment（锁段）的概念，而是启用了一种全新的方式实现,利用CAS算法。它沿用了与它同时期的HashMap版本的思想，底层依然由“数组”+链表+红黑树的方式思想，但是为了做到并发，又增加了很多辅助的类，例如TreeBin，Traverser等对象内部类。



