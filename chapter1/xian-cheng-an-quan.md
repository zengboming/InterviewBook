#### 线程安全

就是多线程访问时，采用了加锁机制，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用。不会出现数据不一致或者数据污染。

要认识java线程安全，必须了解两个主要的点：java的内存模型，java的线程同步机制。

#### JAVA内存模型

Java 内存模型规定和指引Java 程序在不同的内存架构、CPU 和操作系统间有确定性地行为。

主内存：堆空间和方法区

工作内存：栈和寄存器。

![](/assets/20141021095329497.jpg)

1. **可见性**
   多个线程之间是不能互相传递数据通信的，它们之间的沟通只能通过共享变量来进行。Java内存模型规定了JVM有主内存，主内存是多个线程共享的。当new一个对象的时候，也是被分配在主内存中，每个线程都有自己的工作内存，工作内存存储了主存的某些对象的副本。当一个共享变量在多个线程的工作内存中都有副本时，如果一个线程修改了这个共享变量，那么其他线程应该能够看到这个被修改后的值，这就是多线程的可见性问题。
2. **有序性**  
   Java内存模型中，允许编译器和处理器对指令进行重排序，但是重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。

   重排序：编译器在不改变单线程程序语义的前提下，可以重新安排语句的执行顺序。

3. **原子性**

   对基本数据类型的变量的读取和赋值操作是原子性操作，即这些操作是不可被中断的，要么执行，要么不执行。

   只有简单的读取、赋值（变量之间的相互赋值不是原子操作）才是原子操作。

要想并发程序正确地执行，必须要保证原子性、可见性以及有序性。

当线程操作某个对象时，执行顺序如下：

1. 从主存复制变量到当前工作内存 \(read and load\)
2. 执行代码，改变共享变量值 \(use and assign\)
3. 用工作内存数据刷新主存相关内容 \(store and write\)

#### JAVA同步机制

1. **Synchronized关键字**  
   保证了多个线程对于同步块是互斥的，synchronized作为一种同步手段。当一段代码会修改共享变量，这一段代码成为互斥区或临界区，为了保证共享变量的正确性，synchronized标示了临界区。一个没有共享的对象作为锁是没有意义的。每个锁对象都有两个队列，一个是就绪队列，一个是阻塞队列，就绪队列存储了将要获得锁的线程，阻塞队列存储了被阻塞的线程，当一个被线程被唤醒 \(notify\)后，才会进入到就绪队列，等待cpu的调度。synchronized既保证了多线程的并发有序性，又保证了多线程的内存可见性。  
   1. 原子性：synchronized能够保证任一时刻只有一个线程执行该代码块，从而保证了原子性。  
   2. 可见性：synchronized能保证同一时刻只有一个线程获取锁然后执行同步代码，并且在释放锁之前会将对变量的修改刷新到主存当中。  
   3. 有序性：synchronized保证每个时刻是有一个线程执行同步代码，相当于是让线程顺序执行同步代码。

   一个线程执行临界区代码过程如下：  
   1. 获得同步锁  
   2. 清空工作内存  
   3. 从主存拷贝变量副本到工作内存  
   4. 对这些变量计算  
   5. 将变量从工作内存写回到主存  
   6. 释放锁

2. **volatile变量**

   volatile是java提供的一种同步手段，只不过它是轻量级的同步，他可以保证可见性，一定程度上保证有序性，不保证原子性。volatile禁止进行指令重排序。

   1. 可见性：当一个共享变量被volatile修饰时，它会保证修改的值会立即被更新到主存，当有其他线程需要读取时，它会去内存中读取新值。

   2. 不能保证原子性。

   3. 一定程度上保证有序性：volatile禁止进行指令重排序（）。意味着：

      1\) 当程序执行到volatile变量的读操作或者写操作时，在其前面的操作的更改肯定全部已经进行，且结果已经对后面的操作可见；在其后面的操作肯定还没有进行；

      2\) 在进行指令优化时，不能将在对volatile变量访问的语句放在其后面执行，也不能把volatile变量后面的语句放到其前面执行。

   使用方法：volatile int a;  
   使用volatile的条件：  
   1. 对变量的写操作不依赖于当前值。 \(比如i++就不行,因为不是原子操作\)  
   2. 该变量没有包含在具有其他变量的不变式中。（变量不需要与其他的状态变量共同参与不变约束。）

   使用volatile的场景：

   1.状态标记量

   ```
   volatile boolean inited = false;
   //线程1:
   context = loadContext();  
   inited = true;              

   //线程2:
   while(!inited ){
   sleep()
   }
   doSomethingwithconfig(context);
   //不加volatile可能会导致，线程1先执行语句2，然后线程2跳出循环，操作context，但context还未初始化
   ```

   ```
   //线程1
   boolean stop = false;
   while(!stop){
       doSomething();
   }

   //线程2
   stop = true;
   //小概率线程1会无限循环，出现的情况是：线程1从主内存读取stop到工作内存，线程2从主内存读取stop到工作内存，然后修改他的值，
   //但是还没来得及写入主存当中，线程2转去做其他事情了。由于线程2未修改主内存，线程1将一直循环下去。
   //(感觉解释不通,除非线程2永远不写入主内存，只要写入主内存，那么线程1读的时候，就会读出主内存的新值)
   ```

   ```
   //加上volatile就可以了，因为：
   // 1.volatile关键字会强制将修改的值立即写入主存。
   // 2.使用volatile关键字的话，当线程2进行修改时，会导致线程1的工作内存中缓存变量stop的缓存行无效。
   // 3.由于线程1的工作内存中缓存变量stop的缓存行无效，所以线程1再次读取变量stop的值时会去主存读取。
   volatile boolean stop = false;
   ```

   1. 保证对于64位long和double的读取是原子性。在JMM中允许虚拟机对未被volatile修饰的64位的long和double读写操作分为2次32位的操作来执行，这也就是所谓的long和double的非原子性协定。

   2. Double check

   ```
   class Singleton{
       private volatile static Singleton instance = null;

       private Singleton() {

       }

       public static Singleton getInstance() {
           if(instance==null) {
               synchronized (Singleton.class) {
                   if(instance==null)
                       instance = new Singleton();
               }
           }
           return instance;
       }
   }
   ```

3. **Lock**  
   采用lock，必须主动释放锁，并且在发生异常时，不会自动释放锁。因此一般来说，使用Lock必须在try{}catch{}块中进行，并且将释放锁的操作放在finally块中进行，以保证锁一定被释放，防止死锁的发生。

   Lock可以保证原子性、可见性、有序性，原因同Synchronized。

   ```
   public static class Account
   {
       private static Lock lock = new ReentrantLock();
       private int balance =0;
       public int getBalance()
       {
           return balance;
       }
       public  void deposit(int amount)
       {
           lock.lock();
           try{
               int newBalance = balance + amount;
               Thread.sleep(4);   
               balance= newBalance;
           }catch (InterruptedException e) {
               e.printStackTrace();
           }
           finally{
               lock.unlock();
           }
       }
   }
   ```

#### Lock和Synchronized区别

1. Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；

2. Synchronized采用的是CPU悲观锁机制，即线程获得的独占锁。Lock用的是乐观锁方式，每次不加锁而是假设没有冲突，而去完成某项操作，如果因为冲突失败就重试，直到成功为止。

3. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock\(\)去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；

4. Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；lock是可中断的锁，而synchronized是不可中断锁。lock只能中断等待锁的线程，不能中断正在执行的线程。

5. 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。

6. 在资源竞争不是很激烈的情况下，Synchronized的性能要优于ReetrantLock，但是在资源竞争很激烈的情况下，Synchronized的性能会下降几十倍，但是ReetrantLock的性能能维持常态；

#### Volatile和Synchronized区别

1. 粒度不同，Volatile针对变量，Synchronized针对对象和类。
2. Synchronized阻塞，Volatile不阻塞。
3. Synchronized保证：原子性、可见性、有序性。Volatile保证：可见性、有序性，不保证原子性。
4. volatile变量是一和更轻量级的同步机制，因为在使用这些变量时不会发生上下文切换和线程调度等操作。

#### CAS无锁算法

CAS（比较与交换，Compare and swap）是一种有名的无锁算法。CAS的语义是“我认为V的值应该为A，如果是，那么将V的值更新为B，否则不修改并告诉V的值实际为多少”，CAS是项**乐观锁**技术，当多个线程尝试使用CAS同时更新同一个变量时，只有其中一个线程能更新变量的值，而其它线程都失败，失败的线程并不会被挂起，而是被告知这次竞争中失败，并可以再次尝试。CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。



