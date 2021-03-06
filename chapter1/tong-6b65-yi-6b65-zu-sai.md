### 同步

同步和异步关注的是**消息通信机制**。（同步必须等待或者主动的去询问IO是否完成）

用户进程触发IO操作需要等待或者轮询的去查看IO操作执行完成才能执行其他操作.

### 异步

指用户进程触发IO操作以后便直接返回开始做自己的事情，而当IO操作已经完成的时候会得到IO完成的通知

### 阻塞

阻塞和非阻塞关注的是程序**等待调用结果时的状态**。

1. 阻塞：阻塞调用是指调用结果返回之前，当前线程会被挂起。函数只有在得到结果之后才会返回。
2. 非阻塞：指调用在不能立刻得到结果之前，该调用不会阻塞当前线程，而是立即返回。

# IO

一般来说 I/O 模型可以分为：同步阻塞，同步非阻塞，异步阻塞，异步非阻塞 四种IO模型

1. **同步阻塞IO：（BIO）**

   用户进程在发起一个IO操作以后，必须等待IO操作的完成，只有当真正完成了IO操作以后，用户进程才能运行。[**Java**](http://lib.csdn.net/base/javase)**传统的IO模型属于此种方式！**

   适用于连接数目较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4之前的唯一选择，但程序直观、简单、易理解。

2. **同步非阻塞IO:（NIO）**

   在此种方式下，用户进程发起一个IO操作以后边可返回做其它事情，但是用户进程需要时不时的询问IO操作是否就绪，这就要求**用户进程不停的去询问**，从而引入不必要的CPU资源浪费。其中**目前JAVA的NIO就属于同步非阻塞IO。**

   适用于连接数目多且连接比较短的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。

3. **异步阻塞IO：**

   **此种方式下是指应用发起一个IO操作以后，不等待内核IO操作的完成，等内核完成IO操作以后会通知应用程序**，这其实就是同步和异步最关键的区别，同步必须等待或者主动的去询问IO是否完成，那么为什么说是阻塞的呢？因为此时是通过select系统调用来完成的，而select函数本身的实现方式是阻塞的，而采用select函数有个好处就是它可以同时监听多个文件句柄，从而提高系统的并发性！

4. **异步非阻塞IO:（AIO）**  
   在此种模式下，**用户进程只需要发起一个IO操作然后立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作**，因为真正的IO读取或者写入操作已经由内核完成了。

   适用于连接数目多且连接比较长的架构，如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK1.7开始支持

#### JAVA **IO和NIO区别**

1. **面向流与面向缓冲：**
   **IO是面向流的，NIO是面向缓冲区的**。Java** IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方**。此外，它不能前后移动流中的数据。如果需要前后移动从流中读取的数据，需要先将它缓存到一个缓冲区。 Java NIO的缓冲导向方法略有不同。**数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动**。这就增加了处理过程中的灵活性。
2. **阻塞与非阻塞IO：**
   Java** IO是阻塞的**。这意味着，**当一个线程调用read\(\) 或 write\(\)时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了**。 Java** NIO的非阻塞模式**，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，该线程可以继续做其他的事情。 非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。线程通常将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。
3. **选择器（Selectors）：**

   Java **NIO的选择器允许一个单独的线程来监视多个输入通道**，你可以注册多个通道使用一个选择器，然后使用一个单独的线程来“选择”通道：这些通道里已经有可以处理的输入，或者选择已准备写入的通道。这种选择机制，使得一个单独的线程很容易来管理多个通道。

#### 多路IO复用

1. 单个线程，通过记录跟踪每个I/O流的状态，来同时管理多个I/O流 。可以尽量多的提高服务器的吞吐能力。
2. linux下网络io使用socket套接字来通信，普通io模型只能监听一个socket，而io多路复用可同时监听多个socket

3. io多路复用避免阻塞在io上，原本为多进程或多线程来接收多个连接的消息变为单进程或单线程保存多个socket的状态后轮询处理

4. select, poll, epoll 都是I/O多路复用的具体的实现

ngnix会有很多链接进来， epoll会把他们都监视起来，然后像拨开关一样，谁有数据就拨向谁，然后调用相应的代码处理。

#### Select

1. select 会修改传入的参数数组

2. select 如果任何一个sock\(I/O stream\)出现了数据，select 仅仅会返回，但是并不会告诉你是那个sock上有数据，于是你只能自己一个一个的找

3. select 只能监视1024个链接

4. select 不是线程安全的

#### poll

1. poll 去掉了1024个链接的限制

2. poll 从设计上来说，不再修改传入数组，不过这个要看你的平台

#### epoll

1. epoll 现在是线程安全的。
2. epoll 现在不仅告诉你sock组里面数据，还会告诉你具体哪个sock有数据，你不用自己去找了。

![](/assets/importIO.png)

