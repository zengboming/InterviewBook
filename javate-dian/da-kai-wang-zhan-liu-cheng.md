### 从浏览器打开百度，这个过程发生了什么

1. 浏览器查询缓存，如果缓存存在跳到第8步；

2. 浏览器尝试从本地的host文件查看是否存在对应IP地址

3. 如果没找到对应IP，则通过DNS域名解析，返回IP地址给浏览器

4. 浏览器打开对服务器的TCP连接（三次握手）。（将消息从你的PC上传到服务器上，需要用到IP协议、ARP协议和OSPF协议。IP协议指定了出发地（你的PC）和目的地（服务器），你的数据会经过一个又一个路由器，OSPF决定了会经过那些路由器（用一种叫路由算法的玩意，找出最佳路径），通过ARP协议从一个路由器传给下一个路由器）

5. 浏览器通过TCP连接发送HTTP请求，请求网页内容；

6. 服务器通过HTTP响应把文件对应页面发送给浏览器

7. TCP连接释放

8. 浏览器将文件进行解析，并将web网页显示给用户。

```
note：这个题目其实是一个很好引导面试官的题目，在我们说完大致过程之后，我们可以选择自己熟悉地方引。比如

1）关于负载均衡，引导负载均衡的类型以及实现负载均衡的算法；

2）比如session定位问题，引出session绑定、session复制、session服务器集群；

3）比如关于缓存我们可以引出二八定律、一致性hash问题。
```


