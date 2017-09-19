# RPC

**RPC**是指远程过程调用，也就是说两台服务器A，B，一个应用部署在A服务器上，想要调用B服务器上应用提供的函数/方法，由于不在一个内存空间，不能直接调用，需要通过网络来表达调用的语义和传达调用的数据。

**RPC**广泛应用在大规模分布式应用中，作用是有助于系统的垂直拆分，使系统更易拓展。Java中的RPC框架比较多，各有特色，广泛使用的有RMI、Hessian、Dubbo，Netty等。RPC还有一个特点就是能够跨语言

1. 服务消费方（client）调用以本地调用方式调用服务；
2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
3. client stub找到服务地址，并将消息发送到服务端；
4. server stub收到消息后进行解码；
5. server stub根据解码结果调用本地的服务；
6. 本地服务执行并将结果返回给server stub；
7. server stub将返回结果打包成消息并发送至消费方；
8. client stub接收到消息，并进行解码；服务消费方得到最终结果。 

RPC 在整个过程中，体现了逐层抽象，将复杂的协议编解码和数据传输封装到了一个函数中。

RPC缺点：单一 RPC 无法实现 push,即推送服务。理由是，RPC 是client 调用 server获取数据，是一个完整的过程，实现不了server调用client。解决方案：让client 既可以调用server上的RPC服务，反之client本身也成为一个RPC服务让Server来调用。

完成RPC 需要两个协议： 对象序列化协议 和 调用控制协议。

Netty框架不局限于**RPC**，更多的是作为一种网络协议的实现框架，比如HTTP，由于**RPC**需要高效的网络通信，就可能选择以Netty作为基础。除了网络通信，**RPC**还需要有比较高效的序列化框架，以及一种寻址方式。如果是带会话（状态）的**RPC**调用，还需要有会话和状态保持的功能。

大体上来说，Netty就是提供一种事件驱动的，责任链式（也可以说是流水线）的网络协议实现方式。网络协议包含很多层次，很多部分组成，如传输层协议，编码解码，压缩解压，身份认证，加密解密，请求的处理逻辑，怎么能够更好的复用，扩展，业界通用的方法就是责任链，

一个请求应答网络交互通常包含两条链，一条链（Upstream）是从传输层，经过一系列步骤，如身份认证，解密，日志，流控，最后到达业务层，一条链（DownStream）是业务层返回后，又经过一系列步骤，如加密等，又回到传输层。
