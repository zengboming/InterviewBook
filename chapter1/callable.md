#### 创建线程的办法

1. 直接继承Thread

2. 实现Runnable接口

这2种方式都有一个缺陷就是：在执行完任务之后无法获取执行结果。如果需要获取执行结果，就必须通过共享变量或者使用线程通信的方式来达到效果，这样使用起来就比较麻烦。而自从Java 1.5开始，就提供了Callable和Future，通过它们可以在任务执行完毕之后得到任务执行结果。

    3.Callable

#### Thread和Runnable区别

1. Thread是类，需要继承，而只能继承一个父类，有局限性。
2. Thread可直接用start\(\)方法启动线程，而Runnable没有start\(\)方法，需要使用new Thread\(object\).start\(\)；
3. Runnable就可以实现资源共享，而通过Thread类实现，各自线程的资源是独立的，不方便共享。

#### Callable和Runnable

```
public interface Runnable {
    public abstract void run();
}
```

```
//callable是一个泛型接口
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

Callable的使用：

```
1. <T> Future<T> submit(Callable<T> task);
2. <T> Future<T> submit(Runnable task, T result);
3. Future<?> submit(Runnable task);
```

#### Future

Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果。必要时可以通过get方法获取执行结果，该方法会阻塞直到任务返回结果。

```
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning); //用来取消任务
    boolean isCancelled();                         //表示任务是否被取消成功
    boolean isDone();                              //表示任务是否已经完成
    V get() throws InterruptedException, ExecutionException;//获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回；
    V get(long timeout, TimeUnit unit)             //用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null。
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

Future提供了三种功能:

1. 判断任务是否完成；

2. 能够中断任务；

3. 能够获取任务执行结果。

#### FutureTask

```
public class FutureTask<V> implements RunnableFuture<V>
```

```
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}
```

FutureTask实现RunnableFuture接口，RunnableFuture继承了Runnable接口和Future接口，所以它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值。

```
public FutureTask(Callable<V> callable) {}
public FutureTask(Runnable runnable, V result) {}
```

#### 使用示例

Future + Callable

```
public class Test {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        Future<Integer> result = executor.submit(task);
        executor.shutdown();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }

        System.out.println("主线程在执行任务");

        try {
            System.out.println("task运行结果"+result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```

Callable + FutureTask

```
public class Test {
    public static void main(String[] args) {
        //第一种方式
        ExecutorService executor = Executors.newCachedThreadPool();
        Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        executor.submit(futureTask);
        executor.shutdown();

        //第二种方式，注意这种方式和第一种方式效果是类似的，只不过一个使用的是ExecutorService，一个使用的是Thread
        /*Task task = new Task();
        FutureTask<Integer> futureTask = new FutureTask<Integer>(task);
        Thread thread = new Thread(futureTask);
        thread.start();*/

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        }

        System.out.println("主线程在执行任务");

        try {
            System.out.println("task运行结果"+futureTask.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }

        System.out.println("所有任务执行完毕");
    }
}
class Task implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        System.out.println("子线程在进行计算");
        Thread.sleep(3000);
        int sum = 0;
        for(int i=0;i<100;i++)
            sum += i;
        return sum;
    }
}
```



