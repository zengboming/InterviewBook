### MyISAM和InnoDB的区别

1. MySQL默认采用的是MyISAM。
2. MyISAM不支持事务，而InnoDB支持。
3. InnoDB支持数据行锁定，MyISAM默认锁定整个表。

4. InnoDB支持外键，MyISAM不支持。

5. InnoDB不支持全文索引，而MyISAM支持。

6. MyISAM引擎使用B+Tree作为索引结构，**叶节点的data域存放的是数据记录的地址**。MyISAM的索引方式也叫做“**非聚集**”的。

7. **在InnoDB中，表数据文件本身就是按B+Tree组织的一个索引结构**，这棵树的叶节点data域保存了完整的数据记录。这种索引叫做

   **聚集索引**。（因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。）

8. 主键索引：InnoDB的数据文件本身就是索引文件。而MyISAM的索引和数据是分开的。

9. 辅助索引：InnoDB的辅助索引data域存储相应记录主键的值而不是地址。而MyISAM的辅助索引和主索引没有多大区别。



