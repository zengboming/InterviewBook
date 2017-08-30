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

### **面向对象的五大基本原则\(solid\)**

1. S**单一职责**`SRP`:Single-Responsibility Principle 一个类,最好只做一件事,只有一个引起它的变化。单一职责原则可以看做是低耦合,高内聚在面向对象原则的引申,将职责定义为引起变化的原因,以提高内聚性减少引起变化的原因。

2. O**开放封闭原则**`OCP`:Open-Closed Principle 软件实体应该是可扩展的,而不是可修改的。对扩展开放,对修改封闭

3. L**里氏替换原则**`LSP`:Liskov-Substitution Principle 子类必须能够替换其基类。这一思想表现为对继承机制的约束规范,只有子类能够替换其基类时,才能够保证系统在运行期内识别子类,这是保证继承复用的基础。

4. I**接口隔离原则**`ISP`:Interface-Segregation Principle 使用多个小的接口,而不是一个大的总接口

5. D**依赖倒置原则**`DIP`:Dependency-Inversion Principle 依赖于抽象。具体而言就是高层模块不依赖于底层模块,二者共同依赖于抽象。抽象不依赖于具体,具体依赖于抽象。

### 面向对象三大基本特征

1. **继承**：使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。
2. **封装**：把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。
3. **多态**：允许将子类类型的指针赋值给父类类型的指针。Java通过方法重写和方法重载实现多态，方法重写：子类重写了父类的同名方法；方法重载：同一个类中，方法的名字相同，但是参数列表不同。

### **Java移位运算符**

1. &lt;&lt;:左移运算符,x &lt;&lt;1,相当于x乘以2\(不溢出的情况下\),低位补0
2. &gt;&gt;:带符号右移,x &gt;&gt;1,相当于x除以2,正数高位补0,负数高位补1
3. &gt;&gt;&gt;:无符号右移,忽略符号位,空位都以0补齐



