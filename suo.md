# 锁

按操作划分：    DML锁、DDL锁

按锁的粒度划分：表级锁、行级锁、页级锁（mysql）

按锁级别划分：    共享锁、排他锁

按加锁方式划分：自动锁、显示锁

按使用方式划分：乐观锁、悲观锁

#### Mysql 锁

MySQL有三种锁的级别：页级、表级、行级。

MyISAM和MEMORY存储引擎采用的是表级锁（table-level locking）。

BDB存储引擎采用的是页面锁（page-level locking），但也支持表级锁。

InnoDB存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。

表级锁：开销小，加锁快；锁定粒度大，发生锁冲突的概率最高,并发度最低。不会出现死锁；表共享读锁（共享锁）、表独占写锁（排他锁）。

行级锁：开销大，加锁慢；锁定粒度最小，发生锁冲突的概率最低,并发度也最高。会出现死锁； 共享锁（读锁） 、 排他锁（写锁）。

页面锁：开销和加锁时间界于表锁和行锁之间；锁定粒度界于表锁和行锁之间，并发度一般。会出现死锁；

表级锁：表共享读锁\(Table Read Lock\)和表独占写锁\(Table Write Lock\)。

```
    可以在读锁上增加读锁，不能在读锁上增加写锁。在写锁上不能增加写锁。

    MyISAM：在执行查询语句\(SELECT\)前,会自动给涉及的所有表加读锁,在执行更新操作 \(UPDATE、DELETE、INSERT 等\)前，会自动给涉及的表加写锁。

    MyISAM 存储引擎的读锁和写锁是互斥的,读写操作是串行的。【写进程先获得锁\(写锁会插队到读锁前\)】

    MyISAM表不太适合于有大量更新操作和查询操作应用。

    MYSQL将会优先执行写操作。这样MyISAM表在进行大量的更新操作时，会造成查询操作很难获得读锁，从而导致查询阻塞。

    解决办法：

    a）通过指定启动参数low-priority-updates，使MyISAM引擎默认给予读请求以优先的权利。

    b）通过执行命令SET LOW\_PRIORITY\_UPDATES=1，使该连接发出的更新请求优先级降低。

    c）通过指定INSERT、UPDATE、DELETE语句的LOW\_PRIORITY属性，降低该语句的优先级。

    d）在一个有大数据量高并发表的mysql里，我们还可采用另一种策略来进行优化，那就是通过mysql主从（写）    分离来实现负载均衡，这样可避免优先哪一种操作从而可能导致另一种操作的堵塞。\[最好办法\]

    读写分离：主从设置。主服务器负责网站NonQuery操作，从服务器负责Query操作。你的任何改变主服务器数据库的操    作，都会同步到从服务器上。
```

行级锁：共享锁 、 排他锁。

```
    行级锁并不是直接锁记录，而是锁索引。索引分为主键索引和非主键索引两种。

    当两个事务同时执行，一个锁住了主键索引，在等待其他相关索引。另一个锁定了非主键索引，在等待主键索引。这样    就会发生死锁。

    发生死锁后，InnoDB一般都可以检测到，并使一个事务释放锁回退，另一个获取锁完成事务。

    可以在共享锁上增加共享锁，不能在共享锁上增加排他锁。不能再排他锁上增加任何锁。
```

意向锁：意向锁是表级锁，其设计目的主要是为了在一个事务中揭示下一行将要被请求锁的类型。

```
    InnoDB中的两个表锁：意向共享锁（事务准备给数据行加入共享锁）、意向排他锁（事务准备给数据行加入排他锁

    意向锁是InnoDB自动加的，不需要用户干预。
```

悲观锁：假定会发生并发冲突。先取锁再访问。让数据库产生额外的开销，增加产生死锁的机会。主要用于数据争用激烈的环境，以及发生并发冲突时使用锁保护数据的成本要低于回滚事务的成本的环境中。

乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。不会产生任何锁和死锁。适用于多读，提高吞吐量。实现方式：版本号、时间戳
