### linux网络管理

#### ping命令

```shell
ping www.baidu.com 
```

可以看网络对应两个linux的端口通不通

可以设置ping时发几个数据包,每个数据包间隔多久,也可以做压力测试,不停发数据包

发送的时候是发送icmp协议数据包,会有成功返回的信息

该信息包含最大返回时间,最小方差抖动时间以及接收了几个包,发送了几个包来分析丢包率

当然你也可以设置一个icmp包的大小,太大了对面服务器防火墙也会给你拦下来




## linux部署
##### 开启防火墙对应端口
```shell
firewall-cmd --zone=public --add-port=3306/tcp -permanent
firewall-cmd -reload
```

##### 查看服务运行状态
```shell
systemctl status mysqld
```

