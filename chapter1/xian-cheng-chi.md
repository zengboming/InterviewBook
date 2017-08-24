# 线程池

![](/assets/threadpool.png)

#### 为什么要使用线程池

1. 避免频繁地创建和销毁线程，达到线程对象的重用。（创建和销毁线程的开销还是很大的）
2. 使用线程池还可以根据项目灵活地控制并发的数目。

#### 线程池实现原理

**预先启动一些线程，线程无限循环从任务队列中获取一个任务进行执行，直到线程池被关闭。如果某个线程因为执行某个任务发生异常而终止，那么重新创建一个新的线程而已。如此反复。**

线程池的作用是有效的降低频繁创建销毁线程所带来的额外开销。一般来说，线程池都是采用预创建的技术,在应用启动之初便预先创建一定数目的线程。应用在运行的过程中，需要时可以从这些线程所组成的线程池里申请分配一个空闲的线程，来执行一定的任务。任务完成后，并不是将线程销毁，而是将它返还给线程池，由线程池自行管理。如果线程池中预先分配的线程已经全部分配完毕，但此时又有新的任务请求,则线程池会动态的创建新的线程去适应这个请求，但线程的数目永远不会超过最大值。（超过最大值的线程可以排队，但他们要等到其他线程完成后才启动。）

当然，有可能某些时段应用并不需要执行很多的任务，导致了线程池中的线程大多处于空闲的状态，为了节省系统资源，线程池就需要动态的销毁其中的一部分空闲线程。因此，线程池都需要一个管理者，按照一定的要求去动态的维护其中线程的数目。

#### 线程池结构

1. Workqueue：任务队列，用于存放待执行任务。
2. Worker：工作类，一个worker代表启动了一个线程，它启动后会循环执行workQueue里面的所有任务。

#### 线程池执行流程

1. 一个任务提交，如果线程池大小没达到corePoolSize，则每次都**创建**一个worker也就是一个线程来立即执行。

2. 如果达到corePoolSize，则把多余的任务放到workQueue，等待已创建的worker来循环执行。

3. 如果队列workQueue都放满了还没有执行，则在maximumPoolSize下面**创建新**的worker来循环执行workQueue。

4. 如果启动到maximumPoolSize还有任务进来，线程池已达到满负载，此时就执行任务拒绝RejectedExecutionHandler

```
// 流程就是：没达到corePoolSize，创建worker执行，达到corePoolSize加入workQueue
//           workQueue满了且在maximumPoolSize下，创建新worker，达到maximumPoolSize，执行reject
public void execute(Runnable command) {
    if (command == null)
        throw new NullPointerException();

    // 1：poolSize达到corePoolSize，执行3把任务加入workQueue
    // 2：poolSize没达到，执行addIfUnderCorePoolSize()在corePoolSize内创建新worker立即执行任务
    //    如果达到corePoolSize，则同上执行3
    if (poolSize >= corePoolSize || !addIfUnderCorePoolSize(command)) {
        // 3:workQueue满了，执行5
        if (runState == RUNNING && workQueue.offer(command)) {
            if (runState != RUNNING || poolSize == 0) {
                // 4:如果线程池关闭，执行拒绝策略
                //   如果poolSize==0，新启动一个线程执行队列内任务
                ensureQueuedTaskHandled(command);
            }
            // 5:在maximumPoolSize内创建新worker立即执行任务
            //   如果达到maximumPoolSize，执行6拒绝策略
        } else if (!addIfUnderMaximumPoolSize(command))
            // 6:拒绝策略
            reject(command); // is shutdown or saturated
    }
}
```

#### 线程池主要应用场景

1. 需要大量的线程来完成任务，且完成任务的时间比较短，如WEB服务器完成网页请求这样的任务。因为单个任务小，而任务数量巨大。

2. 对性能要求苛刻的应用，比如要求服务器迅速相应客户请求。

#### 线程池参数

1. **keepAliveTime**：代表的就是线程空闲后多久后销毁，线程的销毁是通过worker的getTask\(\)来实现的。一般来说，Worker会循环获取getTask\(\)，如果getTask\(\)返回null则工作线程worker终结。

2. **corePoolSize**\(线程池的基本大小\)：核心线程数，核心线程会一直存活,即使没有任务需要处理。当线程数小于核心线程数时，即使现有的线程空闲，**线程池也会优先创建新线程来处理任务，**而不是直接交给现有的线程处理。

3. **maximumPoolSize**\(线程池最大大小\)：当线程数大于或等于核心线程数，且任务队列已满时，线程池会创建新的线程，直到线程数量达到maxPoolSize。如果线程数已等于maxPoolSize，且任务队列已满，则已超出线程池的处理能力，线程池会拒绝处理任务而抛出异常。

4. **queueCapacity**\(任务队列容量\):从maxPoolSize的描述上可以看出,任务队列的容量会影响到线程的变化,因此任务队列的长度也需要恰当的设置。

5. unit：参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性。
6. workQueue：一个阻塞队列，用来存储等待执行的任务，这个参数的选择也很重要，会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：
   1. ArrayBlockingQueue
   2. PriorityBlockingQueue使用较少
   3. 一般使用LinkedBlockingQueue和Synchronous。
7. threadFactory：线程工厂，主要用来创建线程；
8. handler：表示当拒绝处理任务时的策略，有以下四种取值：
   1. ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
   2. ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。 
   3. ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
   4. ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务 

```
public class ThreadPoolExecutor extends AbstractExecutorService {
    .....
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue);

    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory);

    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler);

    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
        BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler);
    ...
}
```

#### 线程池调优

1. **调整线程池的大小** - 线程池的最佳大小取决于可用处理器的数目以及工作队列中的任务的性质。

2. **CPU限制的任务，提高CPU利用率**：在运行于具有 N 个处理器机器上的计算限制的应用程序中，在线程数目接近 N 时添加额外的线程可能会改善总处理能力，而在线程数目超过 N 时添加额外的线程将不起作用。事实上，太多的线程甚至会降低性能，因为它会导致额外的环境切换开销。

3. **I/O限制的任务（例如，从套接字读取 HTTP 请求的任务）：**需要让池的大小超过可用处理器的数目，因为并不是所有线程都一直在工作。通过使用概要分析，您可以或得一些数据，并计算出大概的线程池大小。Amdahl 法则提供很好的近似公式。用 WT 表示每项任务的平均等待时间，ST 表示每项任务的平均服务时间（计算时间）。则 WT/ST 是每项任务等待所用时间的百分比。对于 N 处理器系统，池中可以近似有**N\*\(1+WT/ST\)**个线程。

#### 常用线程池

1. **Executors.newSingleThreadExecutor\(\)**
   ExecutorService executorService1 = Executors.newSingleThreadExecutor\(\);
   单例线程，任意时间池中只能有一个线程。如果当前线程在执行任务时突然中断，则会创建一个新的线程替代它继续执行任务
2. **Executors.newFixedThreadPool\(\)**  
   ExecutorService executorService2 = Executors.newFixedThreadPool\(10\);  
   创建一个可重用固定线程集合的线程池，以共享的无界队列方式来运行这些线程。

3. **Executors.newScheduledThreadPool\(\)**  
   ExecutorService executorService3 = Executors.newScheduledThreadPool\(10\);  
   调度型线程池。这个池子里的线程可以按schedule依次delay执行，或周期执行。

4. **Executors.newCachedThreadPool\(\)**  
   ExecutorService executorService = Executors.newCachedThreadPool\(\);  
   创建一个可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它们。缓存型池子通常用于执行一些生存期很短的异步型任务。超过TIMEOUT不活动，其会自动被终止。

```
  import java.util.concurrent.ExecutorService;  
  import java.util.concurrent.Executors;  
  public class ThreadPoolTest {  
      public static void main(String[] args) {  
          ExecutorService threadPool = Executors.newFixedThreadPool(3);  
          //ExecutorService threadPool = Executors.newSingleThreadExecutor();
          //ExecutorService threadPool = Executors.newCachedThreadPool();
          for(int i = 1; i < 5; i++) {  
              final int taskID = i;  
              threadPool.execute(new Runnable() {  
                  public void run() {  
                      for(int i = 1; i <= 5; i++) {  
                          try {  
                              Thread.sleep(20);// 为了测试出效果，让每次任务执行都需要一定时间  
                          } catch (InterruptedException e) {  
                              e.printStackTrace();  
                          }  
                          System.out.println("第" + taskID + "次任务的第" + i + "次执行");  
                      }  
                  }  
              });  
          }  
          threadPool.shutdown();// 任务执行完毕，关闭线程池  
      }  
  }
```

```
  import java.util.concurrent.ScheduledExecutorService;  
  import java.util.concurrent.TimeUnit;  
  public class ThreadPoolTest {  
      public static void main(String[] args) {  
          ScheduledExecutorService schedulePool = Executors.newScheduledThreadPool(1);  
          // 5秒后执行任务  
          schedulePool.schedule(new Runnable() {  
              public void run() {  
                  System.out.println("爆炸");  
              }  
          }, 5, TimeUnit.SECONDS);  
          // 5秒后执行任务，以后每2秒执行一次  
          schedulePool.scheduleAtFixedRate(new Runnable() {  
              @Override  
              public void run() {  
                  System.out.println("爆炸");  
              }  
          }, 5, 2, TimeUnit.SECONDS);  
      }  
  }
```



