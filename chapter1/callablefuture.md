#### 创建线程的办法

1. 直接继承Thread

2. 实现Runnable接口

这2种方式都有一个缺陷就是：在执行完任务之后无法获取执行结果。如果需要获取执行结果，就必须通过共享变量或者使用线程通信的方式来达到效果，这样使用起来就比较麻烦。而自从Java 1.5开始，就提供了Callable和Future，通过它们可以在任务执行完毕之后得到任务执行结果。

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



