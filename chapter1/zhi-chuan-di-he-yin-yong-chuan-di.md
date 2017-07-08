#### 交换两个数

```
class MyClass {
    private int num;
    public void setNum(int num) { this.num = num; }
    public int  getNum() { return num; }
}
public static void swap(MyClass a,MyClass b){
    int tmp=a.getNum();
    a.setNum(b.getNum());
    b.setNum(tmp);
}
```

**参数传递基本上就是赋值操作。**java中只有值传递没有引用传递，在传递对象时，是传递对象的引用的副本，不是直接传递对象的引用本体。

```
第一个例子：基本类型
void foo(int value) {
    value = 100;
}
foo(num); // num 没有被改变

第二个例子：没有提供改变自身方法的引用类型
void foo(String text) {
    text = "windows";
}
foo(str); // str 也没有被改变

第三个例子：提供了改变自身方法的引用类型
StringBuilder sb = new StringBuilder("iphone");
void foo(StringBuilder builder) {
    builder.append("4");
}
foo(sb); // sb 被改变了，变成了"iphone4"。

第四个例子：提供了改变自身方法的引用类型，但是不使用，而是使用赋值运算符。
StringBuilder sb = new StringBuilder("iphone");
void foo(StringBuilder builder) {
    builder = new StringBuilder("ipad");  //新分配了一个空间为“ipad”，然后指向ipad
}
foo(sb); // sb 没有被改变，还是 "iphone"。
```



