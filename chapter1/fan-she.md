# 反射

反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

#### 功能

1. 在运行时判断任意一个对象所属的类；
   ```
   a.getClass();
   ```
2. 在运行时构造任意一个类的对象；
   ```
   //实例化Class类对象，3种
   Class<?> class1 = null;      class1 = Class.forName("A");
   Class<?> class2 = null;      class2 = new A().getClass();
   Class<?> class3 = null;   class3 = A.class;
   //实例化类对象，2种
   //第一种方法，实例化默认构造方法，调用set赋值
   A a = (A) class1.newInstance();     a.setAge(20);            
   Constructor<?> cons[] = class1.getConstructors();        
   //第二种方法 取得全部的构造函数 使用构造函数赋值
   // cons[1] (int,java.lang.String)
   A a = (A) cons[0].newInstance(20, "Rollen");
   ```
3. 在运行时判断任意一个类所具有的成员变量和方法；
   ```
   Class<?> class1 = null;      class1 = Class.forName("A");
   //取得类的全部属性
   Field[] field = class1.getDeclaredFields();    
   //获取类的全部方法            
   Method method[] = class1.getMethods();
   ```
4. 在运行时调用任意一个对象的方法；
   ```
   //得到要调用的类中的方法(Method)
   Method m1 = clazz.getDeclaredMethod("setAge",int.class);
   //方法调用(invoke)
   m1.invoke(foo.newInstance(), 15);
   ```
5. 生成动态代理。
   如果想要完成动态代理，首先需要定义一个InvocationHandler接口的子类，已完成代理的具体操作。

```
利用反射像private属性赋值：
Field field = flavor.getClass().getDeclaredField("id"); //得到catClass类所有的属性（包括私有属性）
field.setAccessible(true);                              //该方法表示取消Java语言访问检查 
field.set(flavor, id);                                  //为属性赋值（如果没有第二部,当为私有属性赋值的时候，就会报错）
其他方法：
field.get(id);                                          //取私有属性
Class clazz = foo.getClass();                           //得到要调用类的class
Method m1 = clazz.getDeclaredMethod("setAge",int.class);//得到要调用的类中的方法(Method)
m1.invoke(foo.newInstance(), 15);                       //方法调用(invoke)
```



