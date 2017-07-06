# 生产者消费者模式

线程同步模型。调用锁的wait\(\)方法，让线程主动释放锁。执行lock.wait\(\)，那么这个线程会进入到lock的阻塞队列。如果调用 lock.notify\(\)则会通知阻塞队列的某个线程进入就绪队列。

#### **实现方法**

1. wait\(\) / notify\(\)方法
2. await\(\) / signal\(\)方法
3. BlockingQueue阻塞队列方法
4. PipedInputStream / PipedOutputStream



