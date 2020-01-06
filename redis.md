数据结构：

1. string\(字符串\)
2. list\(链表\)
   1. Redis list的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销。
   2. 比如微博的关注列表，粉丝列表
3. set\(集合\)
4. sorted set \(有序集合\)
5. hash（哈希类型）
   1. Hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。 比如我们可以Hash数据结构来存储用户信息，商品信息等等。

### Redis与Memcached的区别与比较

1 、Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，zset，hash等数据结构的存储。memcache支持简单的数据类型，String。

2 、Redis支持数据的备份，即master-slave模式的数据备份。

3 、Redis支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而Memecache把数据全部存在内存之中

