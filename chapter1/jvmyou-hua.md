# JVM参数

| 参数名称 | 含义 | 默认值 | 说明 |
| :--- | :--- | :--- | :--- |
| -Xms | 初始堆大小 | 物理内存的1/64（&lt;1G） | 默认空余堆内存小于40%\(MinHeapFreeRatio\)时，JVM就会增大堆直到-Xmx的最大限制. |
| -Xmx | 最大堆大小 | 物理内存的1/4（&lt;1G） | 默认空余堆内存大于70%\(MaxHeapFreeRatio\)时，JVM会减少堆直到 -Xms的最小限制 |
| -Xmn | 年轻代大小 |  | 此处的大小是eden+ 2 survivor 。增大年轻代后，将会减小年老代大小。Sun官方推荐配置为整个堆的3/8 |
| -XX:PermSize | 设置持久代初始值 | 物理内存的1/64 |  |
| -XX:MaxPermSize | 设置持久代最大值 | 物理内存的1/64 |  |
| -XX:SurvivorRatio | Eden与Survivor的大小比值 | 8 | 设置为8,则两个Survivor区与一个Eden区的比值为2:8,一个Survivor区占整个年轻代的1/10 |
| -Xss | 每个线程的堆栈大小 |  | JDK5.0以后每个线程堆栈大小为1M,以前每个线程堆栈大小为256K. |

#### 参数设置

1. 编译器设置JVM参数（Eclipse/IDEA）:   -Xmx1024m -Xms1024m
2. 在Tomcat服务器上设置JVM参数：set JAVA\_OPTS=-Xmx512m -Xms512m



