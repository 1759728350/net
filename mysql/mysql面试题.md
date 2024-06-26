
[详见](https://blog.csdn.net/adminpd/article/details/122910606?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171270929516800211512641%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171270929516800211512641&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-122910606-null-null.142^v100^pc_search_result_base3&utm_term=mysql%E9%9D%A2%E8%AF%95%E9%A2%98&spm=1018.2226.3001.4187)
面试题:力求精简,一句话概括
结合实际案例,进行深度剖析


[经典一百道面试题](https://blog.csdn.net/cyl101816/article/details/117520437?ops_request_misc=&request_id=&biz_id=102&utm_term=mysql%E9%9D%A2%E8%AF%95%E9%A2%98&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-117520437.nonecase&spm=1018.2226.3001.4187)
### 表字段
##### mysql字段的数值类型有哪些?
有包括 TINYINT、SMALLINT、INT、BIGINT，分别表示 1 字节、2 字节、4 字节、8 字节的整数类型。

任何整数类型都可以加上 UNSIGNED 属性，表示无符号整数。

还有包括 FLOAT、DOUBLE、DECIMAL 在内的小数类型。



##### 字段的字符串类型有哪些?
包括 VARCHAR、CHAR、TEXT、BLOB。

**注意：VARCHAR(n) 和 CHAR(n) 中的 n 并不代表字节个数，而是代表字符的个数。**


##### **日期和时间类型**

常用于表示日期和时间类型为 DATETIME、DATE 和 TIMESTAMP。

尽量使用 TIMESTAMP，空间效率高于 DATETIME。

##### CHAR 和 VARCHAR 区别？
首先可以明确的是 CHAR 是定长的，会使用空格对字符串右边进行尾部填充,  而 VARCHAR需要拿一个字节表示长度所以 是可以变长。

使用CHAR的情况：

1. 当存储的数据长度固定且不变时，可以使用CHAR。例如，存储邮政编码、身份证等固定长度的数据。
2. 定长,查询速度快, 排序和定值比较快

使用VARCHAR的情况：

1. 当存储的数据长度不确定或可变时，应该使用VARCHAR。例如，存储用户输入的文本、评论内容等。

### 索引
##### 创建索引的优点

1. **提高检索速度,快速定位**：不需要进行全表扫描,只需要找对应字段的索引树就能直接定位到数据行

2. 因为定位快了,所以 ,**排序分组,多表连接查询**时间都会变得更少

4. **减少磁盘IO消耗**：通过使用索引，可以减少数据库系统需要读取的数据量，从而降低磁盘IO的消耗，提升整体性能。
<font color=#99CCFF style=" font-weight:bold;">
总结: 快速定位,降低IO
</font>
##### 创建索引的缺点
- 维护起来会麻烦

- 索引会占很多空间

##### 怎么设计索引

<font color=#99CCFF style=" font-weight:bold;">总结: 给查询条件排序条件建立索引, 控制索引数量,小表没必要建</font>
经常需要 ORDER BY、GROUP BY、DISTINCT 等操作

##### 哪些情况不能用索引
- 数据量少的不适合加索引
    
- 更新比较频繁的也不适合加索引
    
- 区分度低的字段不适合加索引（如性别）
<font color=#99CCFF style=" font-weight:bold;">数据少, 更新频繁, 数据都一样</font>

##### 索引失效
查询条件里有or
like会导致失效
is null
not in都可能导致失效
全表扫描比索引快


##### 为什么 B+ 树比 B 树更适合应用于数据库索引？

<font color=#99CCFF style=" font-weight:bold;">B+ 树节点上存关键字 , 搜索时占用的内存少, 减少了 IO 次数。</font>
由于索引文件很大因此索引文件存储在磁盘上，B+ 树的非叶子结点只存关键字不存数据，因而单个页可以存储更多的关键字，即一次性读入内存的需要查找的关键字也就越多，磁盘的随机 I/O 读取次数相对就减少了。

<font color=#99CCFF style=" font-weight:bold;">
B+ 树数据都在叶子结点且有链表连接, 遍历起来更简单 , 更加适合范围查找
</font>
B+ 树叶子结点之间用链表有序连接，所以扫描全部数据只需扫描一遍叶子结点，利于扫库和范围查询；B 树由于非叶子结点也存数据，所以只能通过中序遍历按序来扫。也就是说，对于范围查询和有序遍历而言，B+ 树的效率更高。



### 事务
##### 关系型数据库事务的四大隔离级别
- 读未提交（Read Uncommitted）
    
- 读已提交（Read Committed）
    
- 可重复读（Repeatable Read）
    
- 串行化（Serializable）
Mysql默认的事务隔离级别是可重复读(Repeatable Read)

##### 什么是幻读，脏读，不可重复读呢？
- <font color=#99CCFF style=" font-weight:bold;">事务A在执行过程中读取到事务B执行时未提交的数据,这就是脏读</font>
    
- 在一次事务，两个相同的查询，前后查的数据不一样，这就是不可重复读。
    
- 事务A查询一个范围的数据，另一个并发事务B往这个范围中插入/删除了数据，并静悄悄地提交，然后事务A再次查询相同的范围，两次读取得到的结果集不一样了，这就是幻读。

##### 在高并发情况下，如何做到安全的修改同一行数据？

要安全的修改同一行数据，就要保证一个线程在修改时其它线程无法更新这行记录。一般有悲观锁和乐观锁两种方案~

使用悲观锁

悲观锁思想就是，当前线程要进来修改数据时，别的线程都得拒之门外~

比如，可以使用select…for update ~

select * from User where name=‘jay’ for update

以上这条sql语句会锁定了User表中所有符合检索条件（name=‘jay’）的记录。本次事务提交之前，别的线程都无法修改这些记录。

使用乐观锁

乐观锁思想就是，有线程过来，先放过去修改，如果看到别的线程没修改过，就可以修改成功，如果别的线程修改过，就修改失败或者重试。实现方式：乐观锁一般会使用版本号机制或时间戳去实现




### sql语句

#### mysql中有哪些表连接方式

<font color=#99CCFF style=" font-weight:bold;">分为内连接和外连接,内连接返回两个表关联匹配的行 , 外连接 分左表为主的左连接和以右表为主的右连接</font>
