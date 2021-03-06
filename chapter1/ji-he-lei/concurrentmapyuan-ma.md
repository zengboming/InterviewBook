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

ConcurrentHashMap由Segment（桶）和HashEntry（节点）构成。

```
 static final class HashEntry<K,V> {  
     final K key;  
     final int hash;  
     volatile V value;  
     final HashEntry<K,V> next;  
 }
```

put：头插法，因为next指针为final的，不可修改。

remove：如果删除中间结点，则需要将要删除节点的前面所有节点整个复制一遍，最后一个节点指向要删除结点的下一个结点。

get：不加锁，value为volatile类型。不变性的访问不需要同步，从而节约读的时间。

构造函数：传入参数分别为 1、初始容量，默认16 2、装载因子 装载因子用于rehash的判定，就是当ConcurrentHashMap中的元素大于装载因子\*最大容量时进行扩容，默认0.75 3、并发级别 这个值用来确定Segment的个数，Segment的个数是大于等于concurrencyLevel的第一个2的n次方的数。默认值为static final int DEFAULT\_CONCURRENCY\_LEVEL = 16。初始化segmentShift和segmentMask（这两个全局变量在定位segment时的哈希算法里需要使用），默认情况下segmentShift为28，segmentMask为15

#### remove

```
 public V remove(Object key) {  
  hash = hash(key.hashCode());  
     return segmentFor(hash).remove(key, hash, null);  
 }  
//整个操作是先定位到段，然后委托给段的remove操作。当多个删除操作并发进行时，只要它们所在的段不相同，它们就可以同时进行。
//下面是Segment的remove方法实现：
 V remove(Object key, int hash, Object value) {  
     lock();  
     try {  
         int c = count - 1;  
         HashEntry<K,V>[] tab = table;  
         int index = hash & (tab.length - 1);  
         HashEntry<K,V> first = tab[index];  
         HashEntry<K,V> e = first;  
         while (e != null && (e.hash != hash || !key.equals(e.key)))  
             e = e.next;  

         V oldValue = null;  
         if (e != null) {  
             V v = e.value;  
             if (value == null || value.equals(v)) {  
                 oldValue = v;  
                 // All entries following removed node can stay  
                 // in list, but all preceding ones need to be  
                 // cloned.  
                 ++modCount;  
                 HashEntry<K,V> newFirst = e.next;  
                 *for (HashEntry<K,V> p = first; p != e; p = p.next)  
                     *newFirst = new HashEntry<K,V>(p.key, p.hash,  
                                                   newFirst, p.value);  
                 tab[index] = newFirst;  
                 count = c; // write-volatile  
             }  
         }  
         return oldValue;  
     } finally {  
         unlock();  
     }  
 }
```

#### put

```
 V put(K key, int hash, V value, boolean onlyIfAbsent) {  
     lock();  
     try {  
         int c = count;  
         if (c++ > threshold) // ensure capacity  
             rehash();  
         HashEntry<K,V>[] tab = table;  
         int index = hash & (tab.length - 1);  
         HashEntry<K,V> first = tab[index];  
         HashEntry<K,V> e = first;  
         while (e != null && (e.hash != hash || !key.equals(e.key)))  
             e = e.next;  

         V oldValue;  
         if (e != null) {  
             oldValue = e.value;  
             if (!onlyIfAbsent)  
                 e.value = value;  
         }  
         else {  
             oldValue = null;  
             ++modCount;  
             tab[index] = new HashEntry<K,V>(key, hash, first, value);  
             count = c; // write-volatile  
         }  
         return oldValue;  
     } finally {  
         unlock();  
     }  
 }
```

1. 判断value是否为null，如果为null，直接抛出异常。
2. key通过一次hash运算得到一个hash值。将得到hash值向右按位移动segmentShift位，然后再与segmentMask做&运算得到segment的索引j。即segmentFor方法
3. 使用Unsafe的方式从Segment数组中获取该索引对应的Segment对象。向这个Segment对象中put值，这个put操作也基本是一样的步骤（通过&运算获取HashEntry的索引，然后set）。

#### get

```
 V get(Object key, int hash) {  
     if (count != 0) { // read-volatile 当前桶的数据个数是否为0 
         HashEntry<K,V> e = getFirst(hash);  得到头节点
         while (e != null) {  
            if (e.hash == hash && key.equals(e.key)) {  
                 V v = e.value;  
                 if (v != null)  
                     return v;  
                 return readValueUnderLock(e); // recheck  
             }  
             e = e.next;  
         }  
     }  
     return null;  
 }
```

get操作不需要锁。第一步是访问count变量，这是一个volatile变量，由于所有的修改操作在进行结构修改时都会在最后一步写count 变量，通过这种机制保证get操作能够得到几乎最新的结构更新。对于非结构更新，也就是结点值的改变，由于HashEntry的value变量是 volatile的，也能保证读取到最新的值。

1. 和put操作一样，先通过key进行hash确定应该去哪个Segment中取数据。
2. 使用Unsafe获取对应的Segment，然后再进行一次&运算得到HashEntry链表的位置，然后从链表头开始遍历整个链表（因为Hash可能会有碰撞，所以用一个链表保存），如果找到对应的key，则返回对应的value值，如果链表遍历完都没有找到对应的key，则说明Map中不包含该key，返回null

**定位Segment的hash算法：\(hash &gt;&gt;&gt; segmentShift\) & segmentMask**

**定位HashEntry所使用的hash算法：int index = hash & \(tab.length - 1\);**

#### JDK1.8

它摒弃了Segment（锁段）的概念，而是启用了一种全新的方式实现,利用CAS算法。它沿用了与它同时期的HashMap版本的思想，底层依然由“数组”+链表+红黑树的方式思想，但是为了做到并发，又增加了很多辅助的类，例如TreeBin，Traverser等对象内部类。

