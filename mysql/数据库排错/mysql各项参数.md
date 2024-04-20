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
