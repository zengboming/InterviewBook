### 三次握手

建立TCP需要三次握手才能建立，而断开连接则需要四次握手。

三次握手的目的是同步连接双方的序列号和确认号并交换 TCP窗口大小信息。

三次而不是两次的原因：为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。

1. 第一次握手 （SYN=1, seq=x）
   建立连接。客户端发送连接请求报文段，将SYN位置为1，Sequence Number为x；
   然后，客户端进入SYN\_SEND状态，等待服务器的确认；
2. 第二次握手 （SYN=1, ACK=1, seq=y, ACKnum=x+1）
   服务器收到客户端的SYN报文段，需要对这个SYN报文段进行确认，
   设置Acknowledgment Number为x+1\(Sequence Number+1\)；
   同时，自己自己还要发送SYN请求信息，将SYN位置为1，Sequence Number为y；
   服务器端将上述所有信息放到一个报文段（即SYN+ACK报文段）中，一并发送给客户端，
   此时服务器进入SYN\_RECV状态；
3. 第三次握手 （ACK=1，ACKnum=y+1）
   客户端收到服务器的SYN+ACK报文段。然后将Acknowledgment Number设置为y+1，向服务器发送ACK报文段，
   这个报文段发送完毕以后，客户端和服务器端都进入ESTABLISHED状态，完成TCP三次握手。

### 四次挥手

中断连接端可以是Client端，也可以是Server端。

断开四次的原因是，Server收到FIN后可能不立即关闭SOCKET，所以先回一个ACK，等结束了再发FIN

1. 第一次挥手 （FIN=1，seq=x）
   假设Client端发起中断连接请求，也就是向Server发送FIN报文
2. 第二次挥手 （ACK=1，ACKnum=x+1）
   Server不必急着关闭Socket，可以继续发送数据。所以Server先发送ACK。
   Client端接收报文后就进入FIN\_WAIT状态，继续等待Server端的FIN报文。
3. 第三次挥手 （FIN=1，seq=y）
   当Server端确定数据已发送完成，则向Client端发送FIN报文
4. 第四次挥手 （\(ACK=1，ACKnum=y+1）
   Client端收到FIN报文后，发送ACK后进入TIME\_WAIT状态。
   Server端收到ACK后就关闭了，Client端等待了2MSL后依然没有收到回复，TCP连接就这样关闭了。

![](/assets/20160624202428273.jpg)

