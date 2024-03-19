

#### docker作用

不同应用比如mysql,redis运行需要的各种依赖的库以及各种不同的配置参数,如果库版本不同会产生不兼容,所以每个容器中的应用都把依赖打包隔离不相互影响

不同操作系统,a程序依赖的系统函数的是centos,b程序依赖的系统函数用的是ubuntu,docker容器能把其所需要的操作系统函数也一起打包的容器中

#### docker server

docker分为client和server

client主要用来发命令

server主要处理发来的命令,比如docker build将一些应用和配置文件进行打包为一个镜像,然后我们可以将这个镜像推送到类似github叫dockerhub,以后方便直接拉取对应的配置的镜像

当我们拉取完镜像到docker server,我们可以在docker client上用docker run命令将镜像跑起来

一个镜像跑起来就会生成多个容器,容器就是镜像跑起来后生成的进程

容器跑起来后,不会对镜像进行写操作,比如一些镜像内部需要产生一些data数据和log数据,容器并不会将这些数据写到镜像里,而是将这些数据保存到容器本身中

##### dockerfile

根据dockerfile生成镜像,此时使用docker build

或者用一个压缩包来解压出一个镜像,用docker load,当然也可以逆向将镜像打包

第三种获取镜像的方式就是从类似hub的远程镜像服务器拉取,使用docker 

### docker进入容器
```shell
docker exec -it 容器名orid  /bin/bash
```
#### docker查看所有容器
```shell
docker ps -a
```

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

#### 将服务打包成镜像

我们可以用压缩tar包和pull的方式拿别人现成的镜像,但当我们需要用自己的服务的镜像时

我们需要学会怎么将自己写的服务打包成镜像-编写dockerfile

然后把springboot打的jar包和dockerfile一起传到要构建镜像的主机上

然后使用docker build命令构建镜像

```dockerfile
FROM openjdk:8-jdk-slim  ## 基础镜像
LABEL maintainer=hedley
COPY target/*.jar /app.jar ##将当前目录下的target目录下的所有jar包给复制到容器内的/app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
## 容器启动时运行的命令 java -jar 
```

#### 推送镜像到dockerhub

1. 首先，在Docker Hub上创建一个账号。如果已经有账号，请跳过这一步。

2. 在本地使用 `docker login` 命令登录到Docker Hub账号。在命令行中输入以下命令，并按照提示输入用户名和密码：
   ```
   docker login
   ```

3. 然后，给你的镜像打上正确的标签。标签的格式为 `<Docker Hub用户名>/<镜像名称>:<版本号>`。例如，假设你的Docker Hub用户名是 "exampleuser"，你的镜像名称是 "myimage"，版本号是 "v1"，那么标签就是 "exampleuser/myimage:v1"。使用以下命令来给镜像打标签：
   ```
   docker tag <本地镜像名称> <Docker Hub用户名>/<镜像名称>:<版本号>
   ```

4. 最后，使用 `docker push` 命令将镜像推送到Docker Hub上。使用以下命令来推送镜像：
   ```
   docker push <Docker Hub用户名>/<镜像名称>:<版本号>
   ```

5. 执行完上述命令后，Docker会将镜像上传到Docker Hub上，并在你的个人仓库中可见。



