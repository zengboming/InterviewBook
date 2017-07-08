#### HTTP长连接

client和server建立连接，完成一次读写之后，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接

TCP保活功能主要为探测长连接的存活状况。保活：如果一个给定的连接在两小时内没有任何的动作，则服务器就向客户发一个探测报文段。

适用于客户端和服务端通信频繁的场景，例如聊天室，实时游戏等。

#### HTTP短连接

一般只会在client/server间传递一次读写操作。

优点：管理起来比较简单，存在的连接都是有用的连接，不需要额外的控制手段

适用于网页浏览等数据刷新频度较低的场景。

#### HTTP断点续传

HTTP协议中定义了断点续传相关的HTTP头 Range和Content-Range字段。

1. 客户端下载一个1024K的文件，已经下载了其中512K 
2. 网络中断，客户端请求续传，因此需要在HTTP头中申明本次需要续传的片段：Range:bytes=512000-

   这个头通知服务端从文件的512K位置开始传输文件

3. 服务端收到断点续传请求，从文件的512K位置开始传输，并且在HTTP头中增加：   
   Content-Range:bytes 512000-/1024000。并且此时服务端返回的HTTP状态码应该是206，而不是200。

#### HTTP head

1）请求方式、URL

2）Host:请求的web服务器域名地址

3\) User-Agent:HTTP客户端运行的浏览器类型的详细信息。

4\) Accept:指定客户端能够接收的内容类型

5\) Content-Type:表示具体请求中的媒体类型信息。在HTTP中，MIME类型被定义在Content-Type     header中。每个MIME类型由两部分组成，前面是数据的大类别，  
例如声音audio、图象image等，后面定义具体的种类。

#### content-type

MediaType，即是Internet MediaType，互联网媒体类型；也叫做MIME类型，  
在Http协议消息头中，使用Content-Type来表示具体请求中的媒体类型信息。

在HTTP中，MIME类型被定义在Content-Type     header中。每个MIME类型由两部分组成，前面是数据的大类别，  
例如声音audio、图象image等，后面定义具体的种类。

#### HTTP请求方式

1. get查询/获取
   安全的：不修改信息
   幂等的：同一URL的多次请求有同样的结果。并没有那么严格。
   请求数据在url里，用?连接
   url会根据浏览器有不同长度限制，进而限制传送的数据量。
2. post更新
   可能修改服务器上的资源请求。
   请求数据在body中
   没限制，可传大量资源。
   安全性比get高，因为数据在url中可见。
3. Post提交数据的方式： 
   [http://www.cnblogs.com/softidea/p/5745369.html](http://www.cnblogs.com/softidea/p/5745369.html)
   POST 提交数据方案，包含了 Content-Type 和消息主体编码方式两部分
   1. application/x-www-form-urlencoded
   2. multipart/form-data
   3. application/json
        告诉服务端消息主体是序列化后的 JSON 字符串
   4. text/xml

#### 常见状态码

1XX 临时响应

2XX 成功

```
200 ok

201 created 服务器已经创建了文档

202 accept 已经接受请求，但处理未完成

203 非授权信息
```

3XX 重定向

4XX 错误

```
400 bad request

401 unauthorized

403 forbidden

404 not found 

408 超时

409 冲突
```

5XX 服务器错误

```
500 internal server error

503 服务器不可用
```



