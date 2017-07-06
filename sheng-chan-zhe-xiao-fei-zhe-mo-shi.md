# 生产者消费者模式

线程同步模型。调用锁的wait\(\)方法，让线程主动释放锁。执行lock.wait\(\)，那么这个线程会进入到lock的阻塞队列。如果调用 lock.notify\(\)则会通知阻塞队列的某个线程进入就绪队列。

#### **实现方法**

1. wait\(\) / notify\(\)方法
2. await\(\) / signal\(\)方法
3. BlockingQueue阻塞队列方法
4. PipedInputStream / PipedOutputStream

#### 方法一

```
public class Storage {
    //最大容量
    private final int MAX_SIZE = 100;        
    private LinkedList<Object> list = new LinkedList<>();
    
    //生产
    public void produce(int num) {
        synchronized (list) {
            while (list.size() + num > MAX_SIZE) {
                System.out.println("【要生产的产品数量】:" + num + "/t【库存量】:"  
                        + list.size() + "/t暂时不能执行生产任务!"); 
                try{
                    //数量太多，产生阻塞
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();  
                }
            }
            //条件满足时，生产
            for (int i = 1; i <= num; ++i) {
                list.add(new Object());
            }
            System.out.println("【已经生产产品数】:" + num + "/t【现仓储量为】:" + list.size());  
            list.notifyAll();
        }
    }
    
    //消费
    public void consume(int num) {
        synchronized(list) {
            //库存不足
            while(list.size() < num) {
                System.out.println("【要消费的产品数量】:" + num + "/t【库存量】:"  
                        + list.size() + "/t暂时不能执行生产任务!");  
            }
            try  {  
                // 由于条件不满足，消费阻塞  
                list.wait();  
            }  
            catch (InterruptedException e) {  
                e.printStackTrace();  
            }
            
            //条件满足，消费
            for (int i = 1; i <= num; ++i) {
                list.remove();
            }
            System.out.println("【已经消费产品数】:" + num + "/t【现仓储量为】:" + list.size());  
            list.notifyAll(); 
        }
    }
}
```



