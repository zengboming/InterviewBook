# Timer

Timer和TimerTask可以做为实现线程的第三种方式。

Timer是一个调度器，用于安排以后在后台线程中执行的任务。可安排任务执行一次，或者定期重复执行，可以看成一个定时器，可以调度TimerTask。TimerTask是一个抽象类，实现了Runnable接口，所以具备了多线程的能力。

```
import java.util.Timer;  
  import java.util.TimerTask;  
  public class TimerTest {  
      static class MyTimerTask1 extends TimerTask {  
          public void run() {  
              System.out.println("爆炸！！！");  
          }  
      }     
      public static void main(String[] args) {  
          Timer timer = new Timer();  
          timer.schedule(new MyTimerTask1(), 2000);// 两秒后启动任务  
      }  
  } 
```

#### 原理

一个Timer内部包装了“**一个Thread”**和“**一个Task”**队列，这个队列按照一定的方式将任务排队处理，包含的线程在**Timer**的构造方法调用时被启动，这个Thread的run方法无限循环这个Task队列，若队列为空且没发生**cancel**操作，此时会一直等待，如果等待完成后，队列还是为空，则认为发生了cancel从而跳出死循环，结束任务；循环中如果发现任务需要执行的时间小于系统时间，则需要执行，那么根据任务的时间片从新计算下次执行时间，若时间片为0代表只执行一次，则直接移除队列即可。

