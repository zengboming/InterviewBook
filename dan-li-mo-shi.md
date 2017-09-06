#### 懒汉式单例

懒汉式只有当调用getInstance的时候，才回去初始化这个单例。

```
//懒汉式单例模式  线程不安全
public class Singleton {
	private static Singleton instance;
	public Singleton() {}
	public static Singleton getInstance() {
		if (instance == null) {
			instance = new Singleton();	
		}
		return instance;
	}
}

//懒汉式单例模式  线程安全
class SingletonSafe {
	private static SingletonSafe instance;
	public SingletonSafe() {}
	public static synchronized SingletonSafe getInstance() {
		if (instance == null) {
			instance = new SingletonSafe();	
		}
		return instance;
	}
}
//懒汉式单例模式 双重锁校验
class SingletonLock {
	private static volatile SingletonLock instance;
	public SingletonLock() {}
	public static SingletonLock getInstance() {
		if (instance == null) {
			synchronized (SingletonLock.class) {
				if (instance == null) {
					instance = new SingletonLock();	
				}
			}
		}
		return instance;
	}
}
```

#### 饿汉式单例

饿汉式在类创建的同时就已经创建好一个静态的对象供系统使用，以后不再改变，所以天生是线程安全的。

```
//饿汉式单例模式
//这种方式基于classloder机制避免了多线程的同步问题,
//只要Singleton类被装载了，那么instance就会被实例化（没有达到lazy loading效果）
class SingletonHungry {
	private static SingletonHungry instance = new SingletonHungry();
	public SingletonHungry() {}
	public static SingletonHungry getInstance() {
		return instance;
	}
}

//饿汉式单例
//通过内部静态类实现
//利用了classloder的机制来保证初始化instance时只有一个线程
//即使Singleton类被装载了，instance不一定被初始化。
//因为SingletonHolder类没有被主动使用，只有显示通过调用getInstance方法时，
//才会显示装载SingletonHolder类，从而实例化instance。
class SingletonStaticInnerClass {
	private static class SingletonHolder {
		private static final SingletonStaticInnerClass INSTANCE = new SingletonStaticInnerClass();
	}
	private SingletonStaticInnerClass() {}
	public static final SingletonStaticInnerClass getInstance() {
		return SingletonHolder.INSTANCE;
	}
	
}
```

#### 饿汉式和懒汉式区别

1. 饿汉就是类一旦加载，就把单例初始化完成，保证getInstance的时候，单例是已经存在的了。
   而懒汉比较懒，只有当调用getInstance的时候，才回去初始化这个单例。
2. 线程安全：
   饿汉式天生就是线程安全的，可以直接用于多线程而不会出现问题。
   懒汉式本身是非线程安全的，为了实现线程安全有几种写法，分别是上面的1、2、3，这三种实现在资源加载和性能方面有些区别。
3. 资源加载和性能：
   饿汉式在类创建的同时就实例化一个静态对象出来，不管之后会不会使用这个单例，都会占据一定的内存，但是相应的，在第一次调用时速度也会更快，因为其资源已经初始化完成。
   而懒汉式顾名思义，会延迟加载，在第一次使用该单例的时候才会实例化对象出来，第一次调用时要做初始化，如果要做的工作比较多，性能上会有些延迟，之后就和饿汉式一样了。

#### 单例模式出现多实例的情况

1. 如果单例由不同的类装载器装入，那便有可能存在多个单例类的实例。假定不是远端存取，例如一些servlet容器对每个servlet使用完全不同的类装载器，这样的话如果有两个servlet访问一个单例类，它们就都会有各自的实例。
   ```
   private static Class getClass(String classname)      
                                            throws ClassNotFoundException {     
         ClassLoader classLoader = Thread.currentThread().getContextClassLoader();     
      
         if(classLoader == null)     
            classLoader = Singleton.class.getClassLoader();     
      
         return (classLoader.loadClass(classname));     
      }     
   }  
   ```
2. 如果Singleton实现了java.io.Serializable接口，那么这个类的实例就可能被序列化和复原。不管怎样，如果你序列化一个单例类的对象，接下来复原多个那个对象，那你就会有多个单例类的实例。
   ```
   public class Singleton implements java.io.Serializable {     
      public static Singleton INSTANCE = new Singleton();     
      
      protected Singleton() {     
        
      }     
      private Object readResolve() {     
               return INSTANCE;     
         }    
   }   
   ```





