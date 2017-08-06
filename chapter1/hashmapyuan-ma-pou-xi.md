[http://www.cnblogs.com/chengxiao/p/6059914.html](http://www.cnblogs.com/chengxiao/p/6059914.html)

# HashMap

## JDK1.7

HashMap **数组+链表。**

哈希冲突的解决方案采用了链地址法。

如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap。

```
 Map map = Collections.synchronizedMap(new HashMap());
```

#### 实现原理

HashMap的主干是一个Entry数组。Entry是HashMap的基本组成单元，每一个Entry包含一个key-value键值对。

```
//HashMap的主干数组，可以看到就是一个Entry数组，初始值为空数组{}，主干数组的长度一定是2的次幂
transient Entry<K,V>[] table = (Entry<K,V>[]) EMPTY_TABLE;

static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;//存储指向下一个Entry的引用，单链表结构
    int hash;//对key的hashcode值进行hash运算后得到的值，存储在Entry，避免重复计算

    /**
     * Creates new entry.
     */
    Entry(int h, K k, V v, Entry<K,V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }

     // 判断两个Entry是否相等    
    // 若两个Entry的“key”和“value”都相等，则返回true。    
    // 否则，返回false    
    public final boolean equals(Object o) {    
        if (!(o instanceof Map.Entry))    
            return false;    
        Map.Entry e = (Map.Entry)o;    
        Object k1 = getKey();    
        Object k2 = e.getKey();    
        if (k1 == k2 || (k1 != null && k1.equals(k2))) {    
            Object v1 = getValue();    
            Object v2 = e.getValue();    
            if (v1 == v2 || (v1 != null && v1.equals(v2)))    
                return true;    
        }    
        return false;    
    }    

    // 实现hashCode()    
    public final int hashCode() {    
        return (key==null   ? 0 : key.hashCode()) ^    
               (value==null ? 0 : value.hashCode());    
    }    
}
```

**简单来说，HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的，如果定位到的数组位置不含链表（当前entry的next指向null）,那么对于查找，添加等操作很快，仅需一次寻址即可；如果定位到的数组包含链表，对于添加操作，其时间复杂度依然为O\(1\)，因为最新的Entry会插入链表头部，急需要简单改变引用链即可，而对于查找操作来讲，此时就需要遍历链表，然后通过key对象的equals方法逐一比对查找。所以，性能考虑，HashMap中的链表出现越少，性能才会越好。**

```
transient Entry[] table;//存储元素的实体数组
//实际存储的key-value键值对的个数
transient int size;
//阈值，当table == {}时，该值为初始容量（初始容量默认为16）；当table被填充了，也就是为table分配内存空间后，threshold一般为 capacity*loadFactory。HashMap在进行扩容时需要参考threshold，后面会详细谈到
int threshold;
//负载因子，代表了table的填充度有多少，默认是0.75
final float loadFactor;
//用于快速失败，由于HashMap非线程安全，在对HashMap进行迭代时，如果期间其他线程的参与导致HashMap的结构发生变化了（比如put，remove等操作），需要抛出异常ConcurrentModificationException
transient int modCount;//被修改的次数
```

若:加载因子越大,填满的元素越多,好处是,空间利用率高了,但:冲突的机会加大了.链表长度会越来越长,查找效率降低。

反之,加载因子越小,填满的元素越少,好处是:冲突的机会减小了,但:空间浪费多了.表中的数据将过于稀疏（很多空间还没用，就开始扩容了）

冲突的机会越大,则查找的成本越高.因此,必须在 "冲突的机会"与"空间利用率"之间寻找一种平衡与折衷. 这种平衡与折衷本质上是数据结构中有名的"时-空"矛盾的平衡与折衷.如果机器内存足够，并且想要提高查询速度的话可以将加载因子设置小一点；相反如果机器内存紧张，并且对查询速度没有什么要求的话可以将加载因子设置大一点。不过一般我们都不用去设置它，让它取默认值0.75就好了。

#### 构造函数

```
public HashMap(int initialCapacity, float loadFactor) {
　　　　　//此处对传入的初始容量进行校验，最大不能超过MAXIMUM_CAPACITY = 1<<30(230)
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);

         // 计算出大于 initialCapacity 的最小的 2 的 n 次方值。
        int capacity = 1;//初始容量
        while (capacity < initialCapacity)//确保容量为2的n次幂，使capacity为大于initialCapacity的最小的2的n次幂
            capacity <<= 1;
        
        this.loadFactor = loadFactor;
        //设置HashMap的容量极限，当HashMap的容量达到该极限时就会进行扩容操作
        threshold = (int) (capacity * loadFactor);
        //初始化table数组
        table = new Entry[capacity];
　　　　　
        init();//init方法在HashMap中没有实际实现，不过在其子类如 linkedHashMap中就会有对应实现
}

 public HashMap(int initialCapacity) {
         this(initialCapacity, DEFAULT_LOAD_FACTOR);
 }

public HashMap() {
         this.loadFactor = DEFAULT_LOAD_FACTOR;
         threshold = (int)(DEFAULT_INITIAL_CAPACITY * DEFAULT_LOAD_FACTOR);
         table = new Entry[DEFAULT_INITIAL_CAPACITY];
        init();
}
```

#### Put

1.7 table在创建hashmap时分配空间，而1.8在put的时候分配，如果table为空，则为table分配空间

```
public V put(K key, V value) {
       //如果key为null，存储位置为table[0]或table[0]的冲突链上
        if (key == null)
            return putForNullKey(value);
        // 若“key不为null”，则计算该key的哈希值，然后将其添加到该哈希值对应的链表中。
         int hash = hash(key.hashCode());
     //搜索指定hash值在对应table中的索引
         int i = indexFor(hash, table.length);
     // 循环遍历Entry数组,若“该key”对应的键值对已经存在，则用新的value取代旧的value。然后退出！
         for (Entry<K,V> e = table[i]; e != null; e = e.next) { 
             Object k;
              if (e.hash == hash && ((k = e.key) == key || key.equals(k))) { //如果key相同则覆盖并返回旧值
                  V oldValue = e.value;
                 e.value = value;
                 e.recordAccess(this);
                 return oldValue;
              }
         }
     //修改次数+1
     modCount++;
     //将key-value添加到table[i]处
     addEntry(hash, key, value, i);
     return null;
    }
```

hash函数，它是通过key的hashCode值计算hash码，下面是计算hash码的函数：

```
//计算hash值的方法 通过键的hashCode来计算
    static int hash(int h) {
        // This function ensures that hashCodes that differ only by
        // constant multiples at each bit position have a bounded
        // number of collisions (approximately 8 at default load factor).
        h ^= (h >>> 20) ^ (h >>> 12);
        return h ^ (h >>> 7) ^ (h >>> 4);
    }
```

得到hash码之后就会通过hash码去计算出应该存储在数组中的索引，计算索引的函数如下：

```
static int indexFor(int h, int length) { //根据hash值和数组长度算出索引值
         return h & (length-1);  //这里不能随便算取，用hash&(length-1)是有原因的，这样可以确保算出来的索引是在数组大小范围内，不会超出
     }
```

![](/assets/hashmap.png)

h&（length-1）保证获取的index一定在数组范围内，举个例子，默认容量16，length-1=15，h=18,转换成二进制计算为2,有些版本的对于此处的计算会使用 取模运算，也能保证index一定在数组范围内，不过位运算对计算机来说，性能更高一些（HashMap中有大量位运算）.

#### Resize

通过以上代码能够得知，当发生哈希冲突并且size大于阈值的时候，需要进行数组扩容，扩容时，需要新建一个长度为之前数组2倍的新的数组，然后将当前的Entry数组中的元素全部传输过去，扩容后的新数组长度为之前的2倍，所以扩容相对来说是个耗资源的操作。

```
void resize(int newCapacity) {
        Entry[] oldTable = table;
        int oldCapacity = oldTable.length;
        if (oldCapacity == MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return;
        }

        Entry[] newTable = new Entry[newCapacity];
        transfer(newTable, initHashSeedAsNeeded(newCapacity));
        table = newTable;
        threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
    }
```

#### Get

get方法通过key值返回对应value，如果key为null，直接去table\[0\]处检索。

```
 public V get(Object key) {
　　　　 //如果key为null,则直接去table[0]处去检索即可。
        if (key == null)
            return getForNullKey();
        Entry<K,V> entry = getEntry(key);
        return null == entry ? null : entry.getValue();
 }

 final Entry<K,V> getEntry(Object key) {

        if (size == 0) {
            return null;
        }
        //通过key的hashcode值计算hash值
        int hash = (key == null) ? 0 : hash(key);
        //indexFor (hash&length-1) 获取最终数组索引，然后遍历链表，通过equals方法比对找出对应记录
        for (Entry<K,V> e = table[indexFor(hash, table.length)];
             e != null;
             e = e.next) {
            Object k;
            if (e.hash == hash && 
                ((k = e.key) == key || (key != null && key.equals(k))))
                return e;
        }
        return null;
    }
```

可以看出，get方法的实现相对简单，key\(hashcode\)--&gt;hash--&gt;indexFor--&gt;最终索引位置，找到对应位置table\[i\]，再查看是否有链表，遍历链表，通过key的equals方法比对查找对应的记录。要注意的是，有人觉得上面在定位到数组位置之后然后遍历链表的时候，e.hash == hash这个判断没必要，仅通过equals判断就可以。其实不然，试想一下，如果传入的key对象重写了equals方法却没有重写hashCode，而恰巧此对象定位到这个数组位置，如果仅仅用equals判断可能是相等的，但其hashCode和当前对象不一致，这种情况，根据Object的hashCode的约定，不能返回当前对象，而应该返回null.

“重写equals时也要同时覆盖hashcode.

### JDK 1.8

链的长度大于8转换成红黑树



