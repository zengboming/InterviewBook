**连接池**

连接池优势

1. 降低延迟：如果不采用连接池，每次连接发起Http请求的时候都会重新建立TCP连接\(经历3次握手\)，用完就会关闭连接\(4次挥手\)，如果采用连接池则减少了这部分时间损耗，别小看这几次握手，本人经过测试发现，基本上3倍的时间延迟
2. 支持更大的并发：如果不采用连接池，每次连接都会打开一个端口，在大并发的情况下系统的端口资源很快就会被用完，导致无法建立新的连接

当请求一个新的连接时，如果连接池有有可用的持久连接，连接管理器就会使用其中的一个，而不是再创建一个新的连接。

PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager\(\);

// 将最大连接数增加到200

cm.setMaxTotal\(200\);

// 将每个路由基础的连接增加到20

cm.setDefaultMaxPerRoute\(20\);

//将目标主机的最大连接数增加到50

cm.setMaxPerRoute\(new HttpRoute\(localhost\), 50\);

CloseableHttpClient httpClient = HttpClients.custom\(\) .setConnectionManager\(cm\) .build\(\);

