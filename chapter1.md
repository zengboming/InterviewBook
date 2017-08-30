### finally

不管有木有异常抛出, finally在return返回前执行。finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的。

注意：finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。

finally不执行的几种情况：程序提前终止如调用了System.exit, 病毒，断电。

### final关键字

final修饰的变量是常量，必须进行初始化，可以显示初始化，也可以通过构造进行初始化，如果不初始化编译会报错。

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

