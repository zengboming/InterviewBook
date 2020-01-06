Java中的Object类是所有类的父类，它提供了以下11个方法：

1. public final native Class&lt;?&gt; getClass\(\)
   1. getClass方法是一个final方法，不允许子类重写，并且也是一个native方法。
2. public native int hashCode\(\)
   1. 该方法返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
3. public boolean equals\(Object obj\)

   1. . **如果2个对象使用equals方法进行比较并且相同的话，那么这2个对象的hashCode方法的值也必须相等。**

   2. hashcode相同 不一定equal

4. protected native Object clone\(\) throws CloneNotSupportedException

   1. 由于Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。

5. public String toString\(\)

6. public final native void notify\(\)
7. public final native void notifyAll\(\)
8. public final native void wait\(long timeout\) throws InterruptedException
9. public final void wait\(long timeout, int nanos\) throws InterruptedException
10. public final void wait\(\) throws InterruptedException
11. protected void finalize\(\) throws Throwable { }
    1. 该方法的作用是实例被垃圾回收器回收的时候触发的操作



