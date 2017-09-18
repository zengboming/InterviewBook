# Hibernate

#### 五大核心接口

1. **Session接口**：负责执行被持久化对象的CRUD操作。但需要注意的是Session对象是非线程安全的。

2. **SessionFactory接口：**负责初始化Hibernate。它充当数据存储源的代理，并负责创建 Session对象。

3. **Configuration接口：**负责配置并启动Hibernate，创建SessionFactory对 象。在Hibernate的启动的过程中，Configuration类的实例首先定位映射文档位置、读取配置，然后创建SessionFactory对象。

4. **Transaction接口：**负责事务相关的操作。

5. **Query和Criteria接口：**负责执行各种数据库查询。它可以使用HQL语言或SQL语句两种表达方式。

#### Lazy-load

在Hibernate框架中，当我们要访问的数据量过大时，明显用缓存不太合适， 因为内存容量有限，为了减少并发量，减少系统资源的消耗，这时Hibernate用懒加载机制来弥补这种缺陷，但是这只是弥补而不是用了懒加载总体性能就提高了。我们所说的懒加载也被称为延迟加载，它在查询的时候不会立刻访问数据库，而是返回代理对象，当真正去使用对象的时候才会访问数据库。



