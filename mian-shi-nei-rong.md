GC 内存模型 线程安全 hashmap spring源码 异步IO   jdk1.6 1.7 1.8特性 HTTP  linux常用命令 进程间通信 kill 四要素  索引实现原理 外链接 内连接

那个最难  怎么解决 项目中注重什么  项目中学到什么

网站 高可用 高并发 设计

线程池 并发 util.concurrent

字典树 建树过程

musql锁 范式 事务 wait connect timeout 间隙锁 next-key锁

servlet  生命周期 对象

session cookie区别

long转int丢精度

笔试公式：

图片：输出尺寸=\(输入尺寸-filter尺寸+2\*padding）/stride+1

逻辑页=逻辑地址/页面大小

一个数组中，两两相等，只有一个数是单独的，求这个数：异或 （A Xor A = 0，A Xor 0 = A）

一个数组中（1001个），都不相同，只有一个是相同的，求出这个数：

假设 重复数为A，其余 999个数异或结果为 B

0 - 1000 异或结果为 ：A Xor B

0 - 1001 异或结果为 ：A Xor （A Xor B）

0 - 1000的结果 和 0 - 1001的结果在异或： （A Xor B） Xor （A Xor （A Xor B））  = A Xor B Xor B  = A

60亿个数（0 - 60亿）,有一个数变成了另一个数，找到不存在的那个数：

求0-60亿的和，和现在所有数的和，知道差值，问题转变为：求60亿中重复的数，使用异或求得，然后再减差值

ES优化：[http://blog.csdn.net/opensure/article/details/47617437](http://blog.csdn.net/opensure/article/details/47617437)

[http://www.jianshu.com/p/4c57a246164c](http://www.jianshu.com/p/4c57a246164c)

1. scroll 深度分页开销大，设置最大页
2. 分片和副本
   1. 拥有更多的shard可以提升索引执行能力，并允许通过机器分发一个大型的索引。
      shard = hash\(routing\) % number\_of\_primary\_shards
      分片数取决于你的es集群有多大，以及你想要达到的并发程度。
   2. 拥有更多的replica能够提升搜索执行能力以及集群能力
      副本数量取决于你想要达到多高的数据可用性和你有多少存储空间可用。
3. 结构优化 不存无用数据
4. 禁用不需要的字段 比如score
5. replica 0 可以增加索引能力
6. 使用bulk批量操作 提升效率
7. 查询性能：routing，使用多个集群，每个集群使用不同的 routing。比如用户是一个维度。
   我们使用这种维度进行各种搭配。然后在前端分析查询，把各个不同查询分别引入合适的集群。这样做以后，每个集群只需要很少的机器，从而查询速度够快，慢查询几乎消灭。
8. 索引越来越大，单个shard也很大，查询速度也越来越慢。这时候，是选择分索引还是更多的shards
   1. 使用更多的shards除了有 IO 压力，而且不能进行全部类目查询，因为完全顶不住。
   2. 应使用分索引：比如按照每个大分类一个索引，然后将他们进行合并查询。



