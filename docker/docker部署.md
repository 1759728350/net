
### 怎么分发部署到多个主机
同时docker还可以将服务分发部署到其他容器中

比如一台主机中的多个容器运行多个nginx

或者私有网络中每个主机都会运行一个nginx的容器服务

我们面对的是如何将这些我们已经配置好的容器给其他机器

这时候有两种方法

* 压缩成文件后拷贝过去
* 提交的dockerhub中去

第一种方法可以将镜像打成tar包,然后到其他机器上解压就行了

第二种方法需要将镜像push到网站上,然后到其他机器上pull下来



### docker管理mysql
先设置中文编码,再建表,否则就算改完编码,表还是乱码

主要还是改一个配置文件就行,如果已经将容器内的数据卷和本地建立,那就改本地的对应目录就行
```shell
docker run -d -p 3306:3306 --privileged=true --name hedley_mysql 
-v /hedley/mysql/log:/var/log/mysql
-v /hedley/mysql/data:/var/lib/mysql
-v /hedley/mysql/conf:/etc/mysql/conf.d
-e MYSQL_ROOT_PASSWORD=root
mysql
```
```shell
docker run -d -p 3306:3306 --privileged=true --name hedley_mysql  -v /hedley/mysql/log:/var/log/mysql -v /hedley/mysql/data:/var/lib/mysql -v /hedley/mysql/conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=root mysql
```
##### 打开mysql的客户端
```shell
 docker exec -it hedley_mysql mysql -u root -p
```
将后面的/bin/bash换成mysql -u root -p密码来打开mysql客户端

而不是打开mysql的文件目录

### docker启动redis

* 第一步:pull拉取linux版本的redis镜像
* run镜像,运行容器

```shell
docker run -v /data/redis/redis.conf:/etc/redis/redis.conf
-v /data/redis/data:/data  ## 前面是要挂载的本机目录,后面是容器内目录
-d --name my_redis_first_image ##运行后的容器名
-p 6379:6379
redis:latest    ##镜像名,这个镜像时pull下来的linux版的最新版redis 
redis-server /etc/redis/redis.conf  ##镜像启动命令,redis启动时是在自己容器内启动
									##因此使用的路径是容器内路径
```

* 设置redis的配置文件中的内容以及设置redis的访问密码
* 打开安全组对应端口,开放服务
* 其他服务想使用redis就只要在设置redis所在ip和端口就行了



### docker安装centos7
```shell
docker pull centos:centos7

docker images
```
运行容器
启动centos容器，并把docker上centos的22端口映射到本机60001端口(端口号可以自己指定)
```shell
docker run -itd --name centos-test -p 60001:22  --privileged centos:centos7 /usr/sbin/init 

进入到Centos容器
docker exec -it centos-test /bin/bash

安装ssh服务和网络必须软件
 yum install net-tools.x86_64 -y
 yum install -y openssh-server


A、安装完后重启SSH服务:
systemctl restart sshd

B.安装passwd软件（用于设置centos用户密码，便于用Xshell连)
yum install passwd -y 

设置用户密码
passwd root

```

