### finally

不管有木有异常抛出, finally在return返回前执行。finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的。

注意：finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。

finally不执行的几种情况：程序提前终止如调用了System.exit, 病毒，断电。

### final关键字

final修饰的变量是常量，必须进行初始化，可以显示初始化，也可以通过构造进行初始化，如果不初始化编译会报错。

### finalize

Java 技术允许使用 finalize\(\) 方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。这个方法是由垃圾收集器在确定这个对象没有被引用时对这个对象调用的，但是什么时候调用 finalize 没有保证。

### 内部类和静态内部类

```
public class Enclosingone {
    //非静态内部类
    public class InsideOne {}
    //静态内部类
    public static class InsideTwo{}
}

public class Test {
    public static void main(String[] args) {
    // 构造内部类对象需要外部类的引用
    Enclosingone.InsideOne obj1 = new Enclosingone().new InsideOne();
    // 构造静态内部类的对象
    Enclosingone.InsideTwo obj2 = new Enclosingone.InsideTwo();
    }
}
```

静态内部类不需要有指向外部类的引用。静态内部类不能访问外部类的非静态成员，只能访问外部类的静态成员。

非静态内部类需要持有对外部类的引用。非静态内部类能够访问外部类的静态和非静态成员。

### **Java移位运算符**

1. &lt;&lt;:左移运算符,x &lt;&lt;1,相当于x乘以2\(不溢出的情况下\),低位补0
2. &gt;&gt;:带符号右移,x &gt;&gt;1,相当于x除以2,正数高位补0,负数高位补1
3. &gt;&gt;&gt;:无符号右移,忽略符号位,空位都以0补齐

### JDK1.7新特性

1. 二进制变量的表示,支持将整数类型用二进制来表示，用0b开头。
2. Switch语句支持string类型 
3. Try-with-resource语句 
4. Catch多个异常 说明：Catch异常类型为final； 生成Bytecode 会比多个catch小； Rethrow时保持异常类型
5. 数字类型的下划线表示 更友好的表示方式，不过要注意下划线添加的一些标准，可以参考下面的示例
6. 在可变参数方法中传递非具体化参数,改进编译警告和错误 
7. 泛型实例的创建可以通过类型推断来简化 可以去掉后面new部分的泛型类型，只用&lt;&gt;就可以了。

### JDK1.8新特性

1. 接口的默认方法
   Java 8允许我们给接口添加一个非抽象的方法实现，只需要使用 default关键字即可，这个特征又叫做扩展方法。
2. Lambda 表达式
   在Java 8 中你就没必要使用这种传统的匿名对象的方式了，Java 8提供了更简洁的语法，lambda表达式：
3. 函数式接口
   Lambda表达式是如何在java的类型系统中表示的呢？每一个lambda表达式都对应一个类型，通常是接口类型。而“函数式接口”是指仅仅只包含一个抽象方法的接口，每一个该类型的lambda表达式都会被匹配到这个抽象方法。因为 默认方法 不算抽象方法，所以你也可以给你的函数式接口添加默认方法。
4. 方法与构造函数引用
   Java 8 允许你使用 :: 关键字来传递方法或者构造函数引用，上面的代码展示了如何引用一个静态方法，我们也可以引用一个对象的方法：
   ```
   converter = something::startsWith;
   String converted = converter.convert("Java");
   System.out.println(converted);
   ```
5. Lambda 作用域
   在lambda表达式中访问外层作用域和老版本的匿名对象中的方式很相似。你可以直接访问标记了final的外层局部变量，或者实例的字段以及静态变量。
6. 访问局部变量
   可以直接在lambda表达式中访问外层的局部变量：
7. 访问对象字段与静态变量
   和本地变量不同的是，lambda内部对于实例的字段以及静态变量是即可读又可写。该行为和匿名对象是一致的：
8. 访问接口的默认方法
   JDK 1.8 API包含了很多内建的函数式接口，在老Java中常用到的比如Comparator或者Runnable接口，这些接口都增加了@FunctionalInterface注解以便能用在lambda上。

Java 8 API同样还提供了很多全新的函数式接口来让工作更加方便，有一些接口是来自Google Guava库里的，即便你对这些很熟悉了，还是有必要看看这些是如何扩展到lambda上使用的。

### JDK1.7 1.8区别

HashMap/ConcurrentHashMap

#### 创建对象的方法

1. new
2. 反射   
   调用[Java](http://lib.csdn.net/base/java).lang.Class或者java.lang.reflect.Constructor类的newInstance\(\)实例方法。

   ```
   Employee emp = Employee.class.newInstance();
   ```

3. clone方法  
   无论何时我们调用一个对象的clone方法，jvm就会创建一个新的对象，将前面对象的内容全部拷贝进去。用clone方法创建对象并不会调用任何构造函数。要使用clone方法，我们需要先实现Cloneable接口并实现其定义的clone方法。

   ```
   Employee emp = (Employee) emp3.clone();
   ```

4. 使用反序列化

   当我们序列化和反序列化一个对象，jvm会给我们创建一个单独的对象。在反序列化时，jvm创建对象并不会调用任何构造函数。为了反序列化一个对象，我们需要实现Serializable接口。

   ```
   ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));
   Employee emp = (Employee) in.readObject();
   ```



