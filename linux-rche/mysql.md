
mysql错误日志

### mysql查看各项参数
##### 查mysql服务器的各项配置

比如:缓冲池,最大连接数,数据文件存放路径
```c
show variables
```

##### 查mysql性能状态
```c
show global status
```
可以查看缓存命中率、线程状态、InnoDB 缓冲池命中率

比如
```c
SHOW GLOBAL STATUS LIKE 'Threads%';

 Variable_name                            | Value |
+------------------------------------------+-------+
| Delayed_insert_threads                   | 0     |
| Mysqlx_worker_threads                    | 2     |
| Mysqlx_worker_threads_active             | 0     |
| Performance_schema_thread_classes_lost   | 0     |
| Performance_schema_thread_instances_lost | 0     |
| Slow_launch_threads                      | 0     |
| Threads_cached                           | 1     |
| Threads_connected                        | 1     |
| Threads_created                          | 2     |
| Threads_running                          | 2   

- `Threads_cached`：缓存中可重复使用的线程数为 1。
- `Threads_connected`：当前已连接的客户端线程数为 1。
- `Threads_created`：创建过的线程总数为 2。
- `Threads_running`：当前正在执行的线程数为 2。

根据这些值，可以看到当前只有一个客户端线程连接到数据库，两个线程被创建过但是只有两个线程在运行。
```

### mysql日志
##### 错误日志
```sql
记录启动关闭,错误警告信息
SHOW VARIABLES LIKE 'log_error'
路径在
/var/log/mysql/error.log
```
>什么时候需要查这个文件呢?
>当数据库启动失败
>数据库连接失败
>备份失败
>数据库资源不足,停机了,这些情况就会将其记录在对应日志文件中

##### 二进制日志
```sql
主要用于记录增删改三种sql执行的日志
路径在
/var/lib/mysql
```
>什么场景下查这个文件?
>数据恢复,主从复制,做安全监控,监控特定类型的sql语句

##### 查询日志
```sql
记录每一条sql语句,默认不开启,在/etc.my.cnf中开启
var/lib/mysql下
```

##### 慢查询日志
```sql
主要用于记录超过特定执行时间的select语句
/var/log/mysql/mysql-slow.log
```
>定位比较慢的语句进行优化
>查找潜在威胁到数据库正常运行的语句,占用太多资源
>检查索引需求,查找慢的语句,去建立索引

##### 日志删除
```sql
可以设置,在某个时间点以前的删除,设置日志过期时间来删除日志
```

### mysql主从配置
为啥要用:
>主库用来写,大部分的读业务走从库,从而实现读写分离

原理:
```text
当执行增删改sql时主服务器先将这些sql写入到二进制日志binlog中,然后从服务器有个ioThread将binlog中的记录读取写入到从服务器的relaylog中,然后从服务器还有个线程叫sqlThread,会读取并执行relaylog中的语句,把数据同步到和主库一样  
但是在主从同步时,会给主库加上全局锁,此时主库只读
```

##### 主库配置
```shell
##开启端口
firewall-cmd --zone=public --add-port=3306/tcp -permanent
firewall-cmd -reload

##修改配置文件/etc/my.cnf
server-id=1  ##设置主库id,不能重复
read only=0  ##设置主库不为只读

##重启mysql
systemctl restart mysql
##如果是在docker中
docker restart hedley_mysql

创建一个用户,用户可以从任意主机连接过来,设置密码
create user'itcast'@'%' dentified with mysql native password by '123456'
为该用户分配主从复制权限,*.*表示给该用户对于所有库的所有表权限,%表示任意ip主机
grant replication slave on *.* to 'itcast'@'%'
##查看主库二进制日志坐标
show master status
```

##### 从库配置

```shell
#配置从库文件/etc/my.cnf
server-id=2
read only=1  ##设置从库只读,不可改

systemctl restart mysql

##登录mysql,设置对应主库
change replication source to SOURCE_HOST='A',SOURCE_USER='B',SOURCE_PASSWORD='C',
SOURCE_LOG_FILE='D',SOURCE_LOG_POS='E'

A为主库IP地址
B为主库用户名
C为主库密码
D为主库二进制日志binlog坐标
E为主库binlog偏移位置,通过show msater status查

##查看主从同步状态
show replica status\G
看到Replica_IO_Running:Yes   Replica_SQL_Running:Yes 就是同步成功了
```

学到数据库分库分表
https://www.bilibili.com/video/BV1Kr4y1i7ru?p=163&vd_source=550ff07b6c6d89a356e2df84891d614e