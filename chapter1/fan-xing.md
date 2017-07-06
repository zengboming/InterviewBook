# 泛型

#### 泛型的机制

泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。泛型只是作用于代码编译阶段，在编译过程中，对于正确检验泛型结果后，会将泛型的相关信息擦出，也就是说，成功编译过后的class文件中是不包含任何泛型信息的。泛型信息不会进入到运行时阶段。

```
ArrayList<String> a = new ArrayList<String>();  
ArrayList b = new ArrayList();  
Class c1 = a.getClass();  
Class c2 = b.getClass();  
System.out.println(c1 == c2); //true
```

```
ArrayList<String> a = new ArrayList<String>();  
a.add("hello");  
Class c = a.getClass();  
try{  
    Method method = c.getMethod("add",Object.class);      //反射是运行时，所以可以赋值。
    method.invoke(a,100);  

}catch(Exception e){  
    e.printStackTrace();  
}
System.out.println(a);  //[hello, 100]
```

```
static <T> void printList(List<T> list) {  
    Iterator<T> it = list.iterator();  
    while (it.hasNext()) {  
        System.out.println(it.next());  
    }  
}
```

#### 泛型的优点

1. 更安全  
   把以前只能运行时的类型检查,提到了编译时。 如果类型不匹配编译是不通过,这样把以前只能在运行程序时才能发现的错误。

   在进行类型转换的时候不会抛出异常。

2. 消除强制类型转换  
   消除源代码中的许多强制类型转换。这使得代码更加可读，并且减少了出错机会。所有的强制转换都是自动和隐式的。

3. 提高性能

#### 通配符

类型通配符上限通过形如Box&lt;? extends A&gt;形式定义，相对应的，类型通配符下限为Box&lt;? super A&gt;形式，其含义与类型通配符上限正好相反

```
public static void main(String[] args) {  
    FX<Number> ex_num = new FX<Number>(100);  
    FX<Integer> ex_int = new FX<Integer>(200);  
    getUpperNumberData(ex_num);  
    getUpperNumberData(ex_int);  
}  

public static void getUpperNumberData(FX<? extends Number> temp){  
      System.out.println("class type :" + temp.getClass());  
}  

public static class FX<T> {  
    private T ob;   
    public FX(T ob) {  
    this.ob = ob;  
    }  
}
```



