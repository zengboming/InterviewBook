# 类加载器

类加载器是一个重要的 Java 运行时系统组件，它负责在运行时查找和装入类文件中的类。

#### 类加载器类型

1. 引导类加载器（Bootstrap ClassLoader）

   负责加载java基础类，主要是 %JRE\_HOME/lib/目录下的rt.jar、resources.jar、charsets.jar等。

2. 扩展类加载器（Extension ClassLoader）

   负责加载java扩展类，从 java.ext.dirs 系统属性所指定的目录中加载类库，它的父加载器是 Bootstrap。

3. 系统加载器（App ClassLoader）

   负责加载当前java应用的classpath中的所有类。又叫应用类加载器，其父类是Extension。它是应用最广泛的类加载器。它从环境变量 classpath 或者系统属性 java.class.path 所指定的目录中记载类。

4. 用户自定义类加载器（java.lang.ClassLoader的子类）

   它的父加载器是系统加载器。

#### 类加载的过程

1. 加载
   根据全限定名来获取定义类的二进制字节流,然后将该字节流所代表的静态结构转化为方法区的运行时数据结构,最后在java堆上生成一个代表该类的Class对象,作为方法区这些数据的访问入口.

2. 验证
   主要时为了确保class文件的字节流中包含的信息符合当前虚拟机的要求,并且不会危害虚拟机自身的安全.包含四个阶段的验证过程: 

   1. **文件格式验证**:保证输入的字节流能够正确地解析并存储在方法区之内,格式上符合描述一个java类型信息的要求 

   2. **元数据验证**:字节码语义信息的验证,以保证描述的信息符合java语言规范.验证点有:这个类是否有父类等. 

   3. **字节码验证**:主要是进行数据流和控制流分析,保证被校验类的方法在运行时不会做出危害虚拟机安全的行为. 

   4. **符号引用验证**:对符号引用转化为直接引用过程的验证.

3. 准备
   为类变量分配内存并设置变量的初始值,这些内存在方法区进行分配.

4. 解析
   将虚拟机常量池中的符号引用转化为直接引用的过程.解析主要是针对类或接口,字段,类方法,类几口方法四类.

5. 初始化
   执行静态变量的赋值操作以及静态代码块,完成初识化.初始化过程保证了父类中定义的初始化优先于子类的初始化.但接口不需要执行父类的初始化.

#### JVM加载class文件的机制

JVM 中类的装载是由类加载器（ClassLoader） 和它的子类来实现的，Java 中的类加载器是一个重要的 Java 运行时系统组件，它负责在运行时查找和装入类文件中的类。 由于 Java 的跨平台性，经过编译的 Java 源程序并不是一个可执行程序，而是一个或多个类文件。当 Java 程序需要使用某个类时，JVM 会确保这个类已经被加载、连接\(验证、准备和解析\)和初始化。类的加载是指把类的 .class 文件中的数据读入到内存中，通常是创建一个字节数组读入 .class 文件，然后产生与所加载类对应的 Class 对象。加载完成后，Class 对象还不完整，所以此时的类还不可用。当类被加载后就进入连接阶段，这一阶段包括验证、准备\(为静态变量分配内存并设置默认的初始值\)和解析\(将符号引用替换为直接引用\)三个步骤。最后 JVM 对类进行初始化，包括：1. 如果类存在直接的父类并且这个类还没有被初始化，那么就先初始化父类；2. 如果类中存在初始化语句，就依次执行这些初始化语句。

#### 双亲委派机制（PDM）

类加载过程采取了双亲委托机制\(PDM\)。PDM 更好的保证了 Java 平台的安全性。

双亲委派模型的工作原理是:如果一个类加载器受到了类加载请求,它首先不会自己去尝试加载这个类,而把这个请求委派给父类加载器去完成,每一层次的类加载器都是如此,因此所有的加载请求最终都应该传送到顶层的启动类加载器中,只有当父类加载器反馈自己无法完成加载请求时,加载器才尝试自己加载.这种方式保证了Object类在各个加载器加载环境中都是同一个类。


