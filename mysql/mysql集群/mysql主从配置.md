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