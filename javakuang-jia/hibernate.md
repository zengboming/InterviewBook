Hibernate

#### 五大核心接口

1. **Session接口**：负责执行被持久化对象的CRUD操作。但需要注意的是Session对象是非线程安全的。

2. **SessionFactory接口：**负责初始化Hibernate。它充当数据存储源的代理，并负责创建 Session对象。

3. **Configuration接口：**负责配置并启动Hibernate，创建SessionFactory对 象。在Hibernate的启动的过程中，Configuration类的实例首先定位映射文档位置、读取配置，然后创建SessionFactory对象。

4. **Transaction接口：**负责事务相关的操作。

5. **Query和Criteria接口：**负责执行各种数据库查询。它可以使用HQL语言或SQL语句两种表达方式。



