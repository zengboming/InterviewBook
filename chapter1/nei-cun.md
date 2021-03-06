# 内存管理

#### JVM内存结构

1. 堆
2. 栈
3. 方法区
4. 本地方法栈
5. 程序计数器

![](/assets/22import.png)

#### 堆

* 堆内存在虚拟机启动时创建，堆内存用来存放由 new 创建的对象和数组，在堆中分配的内存，由 Java 虚拟机的自动垃圾回收器来管理。
* 堆内存是所有**线程共享**的一块内存。
* 在堆中产生了一个数组或者对象之后，还可以在栈中定义一个特殊的变量，让栈中的这个变量的取值等于数组或对象在堆内存中的首地址，栈中的这个变量就成了数组或对象的引用变量。引用变量是普通的变量，定义时在栈中分配，引用变量在程序运行到其作用域之外后被释放。而数组和对象本身在堆中分配，即使程序运行到使用 new 产生数组或者对象的语句所在的代码块之外，数组和对象本身占据的内存不会被释放，数组和对象在没有引用变量指向它的时候，才变为垃圾，不能在被使用，但仍然占据内存空间不放，在随后的一个不确定的时间被GC释放掉。
* 堆可以动态地分配内存大小，生存期也不必事先告诉编译器。但存取速度较慢。

#### 栈

* 每个线程对应一个Java栈，每个线程在的方法被执行时都会同时创建一个**栈帧**用于存储局部变量表，操作数栈，动态链接，方法返回地址等信息。每一个方法被调用直至执行完成的过程就对应着一个栈帧在虚拟机中从入栈到出栈的过程。如果线程请求的**栈深度**大于虚拟机所允许的深度就报StackOverflowError。

* 在函数中定义的一些**基本类型的变量和对象的引用变量**都是在函数的栈内存中分配。

* 当在一段代码块定义一个变量时，Java就在栈中为这个变量分配内存空间，当超过变量的作用域后，Java会自动释放掉为该变量分配的内存空间，该内存空间可以立即被另作它用。

* 栈存取速度比堆要快，但存在栈中的数据大小与生存期必须是确定的。

* 是**线程私有**的内存。

#### 方法区

* 用于存储已被虚拟机加载的**类信息、常量、静态变量、即时编译器编译后的代码**等数据。**运行时常量池**是方法区的一部分，用于存放编译期间生成的各种数字常量和符号引用。当方法区无法满足内存分配需求时，会抛出OutOfMemoryError。

* 是**线程共享**内存。

#### 本地方法栈

* 与虚拟机栈相似，不同的在于它是为虚拟机使用到的Native方法服务的。会抛出StackOverflowError和OutOfMemoryError。

* 是**线程私有**的内存。

#### 程序计数器

* 每个线程都有自己独立的程序计数器，在线程创建时创建，用来指向下一条指令的地址。

* 是**线程私有**的内存。

#### 堆和栈的区别

1. **基本类型的变量**和**对象的引用变量**在栈分配；堆用来分配由new创建的对象和数组，堆内存由GC管理。

2. 堆可以动态地分配内存大小，生命周期不用提前确定，但存取速度较慢；内存大小和生命周期必须确定，存取速度快。

3. 堆内存是线程共享的；栈内存是线程私有的。

#### 内存分配

局部变量：\(包括形参\)

```
局部变量的数据存在于栈内存中。栈内存中的局部变量随着方法的消失而消失。
```

成员变量：\(类的内部定义的变量\)

```
成员变量存储在堆中的对象里面，由垃圾回收器负责回收。
```

int a = 1;

int b = 1;

在栈中创建一个变量为a的引用 - 在栈查找是否有1这个值，若没有则存入，并将a指向1 - 创建b的引用变量 - 将b指向1

String str  = new String\("hello world"\);

String str1 = new String\("hello world"\);                     str != str1\(每次new都会创建一个新的对象\)

String str2 = "hello world";                                          str2 == str3 \(指向同一个对象\)

String str3 = "hello world";

String str4 = "hello" + " world";                                   str4 == str2 \(+号连接在编译器确定\)

String str5 = "hello" + new String\(" world"\);               str5 != str2 \(后半段无法在编译期确定\)

* 第一种办法：

  * 在堆上new了一个值为"hello world"的String对象 - 在栈中创建一个引用变量str指向"hello world" 
    * 在堆上new了一个值为"hello world"的String对象 - 在栈中创建一个引用变量str1指向"hello world" 。
  * 一般每个方法的调用都会独立有一个栈来保存str这样的对象的引用变量，在方法返回后，栈会清空，所以引用变量会被清空掉。堆上的对象，如果没有其他的引用变量引用它，就会被Gc在某个合适的时候gc掉。

* 第二种办法：

  * 在栈中创建一个String类的对象引用变量str2 - 然后通过符号引用去字符串常量池里找是否有"hello world",没有就存入字符串常量池，将str2指向"hello world"  - 在栈中创建一个String类的对象引用变量str3，将str3指向"hello world"。 

String str1 = "hello world";

String str2 = " world";

String str3 = "hello" + str2;                           str3 != str1 \(str2是引用，引用的值在程序编译期无法确定，而是运行期动态分配\)

final String str4 = " world";

str5 == str1 \(final修饰的变量在编译时被解析为常量值的一个本地拷贝存储到自己的常量 池中或嵌入到它的字节码流中\)

