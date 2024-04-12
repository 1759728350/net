

## linux部署

##### 查看服务运行状态
```shell
systemctl status mysqld
```


##### 开启防火墙对应端口
```shell
firewall-cmd --zone=public --add-port=3306/tcp -permanent
firewall-cmd -reload
```

