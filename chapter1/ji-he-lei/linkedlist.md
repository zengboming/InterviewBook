offer属于 offer in interface Deque&lt;E&gt;

add 属于 add in interface Collection&lt;E&gt;

作为List使用时,一般采用add / get方法来 压入/获取对象

作为Queue使用时,才会采用 offer/poll/take等方法

当队列为空时候，使用add方法会报错，而offer方法会返回false。

