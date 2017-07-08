# 生产者消费者模式

线程同步模型。调用锁的wait\(\)方法，让线程主动释放锁。执行lock.wait\(\)，那么这个线程会进入到lock的阻塞队列。如果调用 lock.notify\(\)则会通知阻塞队列的某个线程进入就绪队列。

#### **实现方法**

1. wait\(\) / notify\(\)方法
2. await\(\) / signal\(\)方法
3. BlockingQueue阻塞队列方法
4. PipedInputStream / PipedOutputStream

#### 方法一    wait\(\) / notify\(\)方法

```
//仓库类Storage实现缓冲区
public class Storage {

    private int number = 0;
    // 仓库最大存储量 
    private final int MAX_NUM = 5;
    // 仓库存储的载体 
    private LinkedList<Object> list = new LinkedList<>();

    public void produce() throws InterruptedException {
        //同步
        synchronized (list) {
            while (number == MAX_NUM) {
                System.out.println("box is full,size = " + number);
                //阻塞,线程等待,释放锁
                list.wait();
            }

            list.add(new Object());
            number++;
            System.out.println("produce success number = " + number);
            //唤醒当前对象的所有等待的线程
            list.notifyAll();
        }
    }

    public void consume() throws InterruptedException {
        //同步
        synchronized (list) {
            while (number == 0) {
                System.out.println("box is empty,size = " + number);
                //阻塞
                list.wait();
            }

            list.remove();
            number--;
            System.out.println("comsume success number = " + number);
            list.notifyAll();
        }
    }

}
```

```
//生产者类
public class Producer implements Runnable {

    private Storage storage;
    private String name;

    public Producer(Storage storage, String name) {
        this.storage = storage;
        this.name = name;
    }

    @Override
    public void run() {
        while (true) {
            try {
                storage.produce();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```
//消费者类
public class Consumer implements Runnable {

    private Storage storage;
    private String name;

    public Consumer(Storage storage, String name) {
        this.storage = storage;
        this.name = name;
    }

    @Override
    public void run() {
        while (true) {
            try {
                storage.consume();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    } 
}
```

```
public class Main {
    public static void main(String[] args) {

        Storage storage = new Storage();
        new Thread(new Consumer(storage, "消费者1")).start();
        new Thread(new Consumer(storage, "消费者2")).start();
        new Thread(new Consumer(storage, "消费者3")).start();

        new Thread(new Producer(storage, "生产者1")).start();
        new Thread(new Producer(storage, "生产者2")).start();
        new Thread(new Producer(storage, "生产者3")).start();
    }
}
```

#### 方法二    await\(\) / signal\(\)方法

```
//仓库类
public class Storage {

    public final int MAX_NUM = 5;
    private int number = 0;
    // 仓库存储的载体 
    private LinkedList<Object> list = new LinkedList<>();

    //锁
    private final Lock lock = new ReentrantLock();
    //条件变量
    private final Condition condition = lock.newCondition();

    public void produce() {
        lock.lock();
        try {
            while(number == MAX_NUM) {
                System.out.println("box is full,size = " + number);
                condition.await();
            }

            list.add()new Object();
            number++;
            System.out.println("produce success number = " + number);
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }

    }

    public void consume() {
        lock.lock();

        try {
            while (number == 0) {
                System.out.println("box is empty,size = " + number);
                condition.await();
            }

            list.remove();
            number--;
            System.out.println("consume success number = " + number);
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```



