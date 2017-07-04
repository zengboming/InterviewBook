# 垃圾回收机制（GC）

#### GC原理

JAVA中的对象是通过new或反射的方法创建的，这些对象的创建都是在堆（Heap）中分配的，所有对象的回收都是由JAVA虚拟机通过垃圾回收机制完成的。GC为了能正确释放对象，会监控每个对象的运行状况，对他们的申请、引用、被引用、赋值等情况进行监控，JAVA会使用有向图的方式进行管理内存，实时监控对象是否可以达到，如果“不可达”，则将其回收，这样也可以消除引用循环的问题。

在JAVA中，判断一个内存空间是否符合垃圾收集的标准有两个。一个是给对象赋予了Null，以下再没调用过；另一个是给对象赋予了新值，这样重新分配了内存空间。

#### 按代GC机制

垃圾回收器会在下面两种前提成立的情况下被创建：

1. 大多数对象会很快变得不可达
2. 只有很少的由老对象（创建时间较长的对象）指向新生对象的引用

HotSpot虚拟机将其物理上划分为新生代（young generation）、老年代（old generation）和持久代（ permanent generation ）

1. **新生代**（young generation）  
   绝大多数最新被创建的对象会被分配到这里，由于大部分对象在创建后会很快变得不可到达，所以很多对象被创建在新生代，然后消失。对象从这个区域消失的过程：”minor GC“。

   三个空间：  
   1. 1. 一个伊甸园空间（Eden ）  
      2. 两个幸存者空间（Survivor ）

   执行顺序：  
   1. 绝大多数刚刚被创建的对象会存放在伊甸园空间。  
   2. 在伊甸园空间执行了第一次GC之后，存活的对象被移动到其中一个幸存者空间。  
   3. 此后，在伊甸园空间执行GC之后，存活的对象会被堆积在同一个幸存者空间。  
   4. 当一个幸存者空间饱和，还在存活的对象会被移动到另一个幸存者空间。之后会清空已经饱和的幸存者空间。  
   5. 在以上的步骤中重复几次依然存活的对象，就会被移动到老年代。

2. **老年代**（old generation）  
   对象没有变得不可达，并且从新生代中存活下来，会被拷贝到这里。其所占用的空间要比新生代多。也正由于其相对较大的空间，发生在老年代上的GC要比新生代少得多。对象从老年代中消失的过程：”major GC“。  
   老年代空间的GC事件基本上是在空间已满时发生。

3. **持久代**（ permanent generation ）又叫方法区

   用来保存类常量以及字符串常量。这个区域也可能发生GC。并且发生在这个区域上的GC事件也为major GC。

#### GC类型

1. Serial GC                                              单线程的（标记-清除）
2. Parallel GC                                           多线程的
3. Parallel Old GC                                    多线程的（标记-汇总-压缩）
4. Concurrent Mark & Sweep GC           停顿时间短，占用更多的内存和CPU
5. Garbage First \(G1\) GC                        

#### GC交互

JAVA有四种引用类型：StrongReference、SoftReference、WeakReference和 PhantomReference

1. SoftReference
   只有当内存不够的时候，才进行回收这类内存，因此在内存足够的时候，它们通常不被回收。适合某些缓存应用。
2. WeakReference
   Weak引用对象更容易、更快被 GC回收。GC运行时，必回收。
3. PhantomReference
   主要用于辅助 finalize函数的使用。它是最弱的一种引用关系，也无法通过PhantomReference取得对象的实例。仅用来当该对象被回收时收到一个通知。
4. StrongReference

   Java 的默认引用实现, 它会尽可能长时间的存活于 JVM 内，当没有任何对象指向它时将会被GC回收。

#### GC调优

在设计GC的时候，就必须在停顿时间和回收率之间进行权衡。

**Stop-the-world**

Stop-the-world意味着JVM因为要执行GC而停止了应用程序的执行。当Stop-the-world发生时，除了GC所需的线程以外，所有线程都处于等待状态，直到GC任务完成。GC优化很多时候就是指减少Stop-the-world发生的时间。

#### GC收集方法

1. **标记清除**

   首先标记所有需要回收的对象，在标记完成后**统一回收掉**所有被标记的对象，它的标记的对象。缺点是**效率低**，且存在**内存碎片**。主要用于老生代垃圾回收。

2. **标记整理**

   首先标记所有需要回收的对象，在标记完成后让所有存活的对象都向一端移动，然后直接清理掉端边界意外的内存。用于老年代。

3. **复制算法**

   将内存按容量划分为大小相等的一块，每次只用其中一块。当内存用完了，将还存活的对象复制到另一块内存，然后把已使用过的内存空间一次清理掉。实现简单，高效。一般用于新生代。一般是将内存分为一块较大的Eden空间和两块较小的Survivor空间。HotSpot虚拟机默认比例是8:1,。每次使用Eden和一块Survivor，当回收时将这两块内存中还存活的对象复制到Survivor然后清理掉刚才Eden和Survivor的空间。如果复制过程内存不够使用则向老年代分配担保。

#### Minor GC

MinorGC MinorGC 指发生在新生代的垃圾收集动作，非常频繁，回收速度也快。一般发生在新生代空间不足时,另外一个FullGC经常会伴随至少一次的Minor GC. 当虚拟检测晋升到到老年代的平均大小是否小于老年代剩余空间大小,如果小于并且允许担保失败,则执行Minor GC.

#### Full GC

FullGC 一般是发生在老年代的GC，出现一个FullGC经常会伴随至少一次的Minor GC。速度比MinorGC慢10倍以上。

FULL GC发生的情况: 

1.  老年代空间不足 老年代空间只有在新生代对象转入及创建为大对象、大数组时才会出现不足的现象，当执行Full GC后空间仍然不足，则抛出如下错误：java.lang.OutOfMemoryError: Java heap space . 措施:为避免以上两种状况引起的FullGC，调优时应尽量做到让对象在Minor GC阶段被回收、让对象在新生代多存活一段时间及不要创建过大的对象及数组。
2.  Permanet Generation\(方法区或永久代\)空间满 PermanetGeneration中存放的为一些class的信息等，当系统中要加载的类、反射的类和调用的方法较多时，Permanet Generation可能会被占满，在未配置为采用CMS GC的情况下会执行Full GC。如果经过Full GC仍然回收不了，那么JVM会抛出如下错误信息： java.lang.OutOfMemoryError: PermGen space 措施:为避免Perm Gen占满造成Full GC现象，可采用的方法为增大Perm Gen空间或转为使用CMS GC。 
3.  CMS GC时出现promotion failed和concurrent mode failure 对于采用CMS进行老年代GC的程序而言，尤其要注意GC日志中是否有promotion failed和concurrent mode failure两种状况，当这两种状况出现时可能会触发Full GC。 promotion failed是在进行Minor GC时，survivor space放不下、对象只能放入老年代，而此时老年代也放不下造成的； concurrent mode failure: CMS在执行垃圾回收时需要一部分的内存空间并且此刻用户程序也在运行需要预留一部分内存给用户程序，如果预留的内存无法满足程序需求就出现一次"Concurrent mod failure",并触发一次Full GC。 应对措施为：增大survivor space、老年代空间或调低触发并发GC的比率。
4. 统计得到的Minor GC晋升到旧生代的平均大小大于旧生代的剩余空间 Hotspot为了避免由于新生代对象晋升到旧生代导致旧生代空间不足的现象，在进行Minor GC时，做了一个判断，如果之前统计所得到的Minor GC晋升到旧生代的平均大小大于旧生代的剩余空间，那么就直接触发Full GC。如果小于并且不允许担保失败也会发生一次Full GC



