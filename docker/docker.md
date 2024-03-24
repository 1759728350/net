

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

### dockerfile和镜像容器关系
从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，
* Dockerfile是软件的原材料
* Docker镜像是软件的交付品
* Docker容器则可以认为是软件镜像的运行态，也即依照镜像运行的容器实例
Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可

![](img/Pasted%20image%2020240319180734.png)





### dockerfile

Dockerfile定义了进程需要的一切东西。Dockerfle涉及的内容包括执行代码或者是文件、环境
变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程

##### dockerfile有哪些关键字？用途是什么
1. **FROM**：指定基础镜像，即新镜像是基于哪个镜像构建的
2. **RUN**：在<font color=#99CCFF style=" font-weight:bold;">镜像构建时</font>执行的命令(docker build执行时)，用于安装软件、配置环境等操作。可在当前层执行任何命令并创建一个新层，用于在映像层中添加功能层，后来的层会依赖它。
3. **COPY**：将文件或目录从构建上下文复制到镜像中。 比如镜像需要依赖jdk8就可以指定当前目录的jdk8,然后在build时就会拷贝到之前镜像的上层并安装
4. **EXPOSE**：声明容器运行时监听的端口，但并不真正发布该端口。
5. **VOLUME**：创建一个挂载点，用于持久化存储数据
6. ENV: 设置环境变量
7. CMD: 启动容器时执行的命令
8. ENTRYPOINT: 类似于CMD指令，但是ENTRYPOINT<font color=#99CCFF style=" font-weight:bold;">不会被docker run后面的命令覆盖</font>,且会使得CMD变为参数
9. WORKDIR: docker run命令执行后的落脚点目录
10. LABEL： LABEL 指令用于组织项目映像，模块，许可等。在自动化布署方面 LABEL 也有很大用途。在 LABEL 中指定一组键值对，可用于程序化配置或布署 Docker

##### CMD
```shell
CMD ["catalina.sh", "run"]
```
相当于./catalina.sh run
也就是说,当docker run tomcat时,容器内部会执行的就是./catalina.sh run

CMD会被docker run tomcat之后输入的参数替换掉
```shell
docker run -it -p 8080:8080 tomcat /bin/bash
会影响到dockerfile中的CMD关键字,CMD会变成
CMD ["/bin/bash", "run"]
因此,容器内命令就会由./catalina.sh run变为/bin/bash run
容器启动起来了,但是容器内部的tomcat并没有成功启动
```

##### ENTRYPOINT

ENTRYPOINT可以和CMD一起用，一般是变参才会使用CMD，这里的CMD等于是在给ENTRYPOINT传参,<font color=#99CCFF style=" font-weight:bold;">充当参数部分</font>。
当指定了ENTRYPOINT后，CMD的含义就发生了变化，不再是直接运行其命令而是将CMD的内容作为参数传递给ENTRYPOINT指令，他两个组合会变成
```
<ENTRYPOINT> "<CMD>"
```
比如
```shell
ENTRYPOINT ["nginx","-c"]
CMD ["/etc/nginx/nginx.conf"]
启动容器不传参时就会
docker run nginx  ===> 容器内执行命令  nginx -c /etc/nginx/nginx.conf
启动容器时传参就会
docker run nginx -c /etc/nginx/aabb.conf ===>容器内执行命令   nginx -c /etc/nginx/aabb.conf
传参就会覆盖掉CMD指定的参数
```

<font color=#99CCFF style=" font-weight:bold;">CMD参数部分会被docker run之后的参数覆盖</font>


#### json方式编写CMD,RUN
`CMD`指令可以使用另一种格式进行编写。除了使用JSON数组格式如`CMD ["executable","param1","param2"]`
```
CMD ["executable","param1","param2"]
```
还可以使用Shell格式进行编写。在Shell格式下，`CMD`指令可以像下面这样写：

```shell
CMD command param1 param2
```

#### 解读tomcat的dockerfile
```shell
FROM java:7-jre

ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

# see https://www.apache.org/dist/tomcat/tomcat-8/KEYS
RUN set -ex \
	&& for key in \
		05AB33110949707C93A279E3D3EFE6B686867BA6 \
		07E48665A34DCAFAE522E5E6266191C37C037D42 \
		省略....
	; do \
		gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done

ENV TOMCAT_MAJOR 8
ENV TOMCAT_VERSION 8.0.32
ENV TOMCAT_TGZ_URL https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz

RUN set -x \
	&& curl -fSL "$TOMCAT_TGZ_URL" -o tomcat.tar.gz \
	&& curl -fSL "$TOMCAT_TGZ_URL.asc" -o tomcat.tar.gz.asc \
	&& gpg --batch --verify tomcat.tar.gz.asc tomcat.tar.gz \
	&& tar -xvf tomcat.tar.gz --strip-components=1 \
	&& rm bin/*.bat \
	&& rm tomcat.tar.gz*

EXPOSE 8080
CMD ["catalina.sh", "run"]
```

* &&符号
`&&`符号用于将多个命令连接在一起，以便在前一个命令成功执行后才执行下一个命令。每个`&&`符号表示着一个逻辑"与"操作，即前一个命令成功返回退出码（exit code为0）时，才会继续执行下一个命令。如果前一个命令失败（exit code非0），那么后续的命令将不会执行，从而保证了整个命令序列的顺序和可靠性。





### 镜像
##### 如何获取镜像

根据dockerfile生成镜像,此时使用docker build

或者用一个压缩包来解压出一个镜像,用docker load,当然也可以逆向将镜像打包

第三种获取镜像的方式就是从类似hub的远程镜像服务器拉取,使用docker 

#### docker build命令
**根据dockerfile来构建镜像**
```bash
docker build [OPTIONS] PATH | URL | -
```

2. **参数说明**:
    
    - `--tag, -t`: 为生成的镜像指定一个标签（名称:标签），例如`myimage:latest`。
    - `--file, -f`: 指定要使用的Dockerfile路径，如果不指定，默认为当前目录下的`Dockerfile`。
    - `--build-arg`: 设置在构建镜像时使用的构建参数，可以在Dockerfile中使用`${ARG_NAME}`来引用。
    - `--network`: 设置构建过程中使用的网络模式，如`bridge`、`host`、`none`等。
    - `--pull`: 在构建前尝试拉取最新的基础镜像。
    - `--no-cache`: 使用该参数将忽略缓存，从头开始构建镜像。
    - `--quiet, -q`: 减少输出，只显示构建进度信息。
    - `--progress`: 设置输出的构建进度类型，如`plain`、`tty`、`auto`等。
    - `--squash`: 压缩最终镜像的各层，以减少镜像大小。
3. **示例**:
    
    - 构建位于当前目录下的默认`Dockerfile`：
        
        ```bash
        docker build -t myimage:latest .
        ```
        
    - 指定Dockerfile路径并设置构建参数：
        
        ```bash
        docker build -t myimage:latest -f Dockerfile.dev --build-arg APP_ENV=development .
        ```
        
    - 从指定URL拉取Dockerfile并进行构建：
        
        ```bash
        docker build -t myapp:latest https://github.com/username/repository.git#branch
        ```

#### docker commit命令
`docker commit`命令允许用户<font color=#99CCFF style=" font-weight:bold;">基于现有的容器创建一个新的镜像</font>。这个命令可以用于在容器中进行一些修改或者安装新的应用程序，并且将这些修改保存为新的镜像，方便后续的使用和分享。

```bash
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

    - `CONTAINER`: 指定要提交的容器的ID或者名称。
    - `REPOSITORY[:TAG]`: 指定新创建的镜像的名称和标签。
3. **选项说明**：
    - `--change, -c`: 设置容器的元数据，比如作者、环境变量、工作目录等。
    - `--message, -m`: 为新镜像指定提交的说明信息。
4. **示例**：
    - 基于容器ID提交新镜像：
        ```bash
        docker commit mycontainer mynewimage:latest
        ```

### 容器
#### docker进入容器
```shell
docker exec -it 容器名orid  /bin/bash
```
#### docker查看所有容器
```shell
docker ps -a
```

#### exec命令详解
`docker exec`命令用于在运行中的Docker容器内部执行命令。通过这个命令，你可以在容器内部执行特定的命令，而无需进入容器的交互式Shell环境。

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

- `CONTAINER`指定要执行命令的目标容器的ID或名称。
- `COMMAND`及其`ARG`参数指定要在容器内部执行的命令。

一些常用的选项包括：
- `-d, --detach`: 在后台模式下执行命令。
- `-i, --interactive`: 保持STDIN打开，允许交互式输入。
- `-t, --tty`: 分配伪TTY终端，通常与`-i`一起使用。
- `--user`: 指定要执行命令的用户。
- `--env`: 指定环境变量。

例如，要在名为`my-container`的容器内部执行`ls -l`命令，可以使用以下命令：
```
docker exec my-container ls -l
```

`docker exec`命令非常有用，可以让你在运行中的容器内部执行各种操作，如查看容器内部的文件，调试应用程序等，而无需进入容器的Shell环境。


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



### docker网络
1. **Bridge（桥接）**：默认的网络模式，容器通过在同一主机上创建虚拟网桥连接到宿主机。容器可以使用IP地址访问其他容器或外部网络。
    
2. **Host（主机）**：容器与宿主机共享网络命名空间，容器使用宿主机的网络栈，可以直接访问宿主机上的端口。
3. container模式: 和某个容器共享IP和端口资源
4. none模式: 网络都没有配置


#### bridge桥接
1 Docker使用Linux桥接，在宿主机虚拟一个Docker容器网桥(docker0)，Docker启动一个容器时会根据Docker网桥的网段分配给容器一个IP地址，同时Docker网桥是每个容器的默认网关。因为在同一宿主机内的容器都接入同一个网桥，这样容器之间就能够直接通信。**(docker0相当于交换机,也就是这几个容器都是在同一个交换机底下,宿主机相当于路由器,所以在主机ifconfig查看网络配置可以看到三个docker创建的网络和主机连接外网的网络)**

2 docker run 的时候，没有指定network的话默认使用的网桥模式就是bridge，使用的就是docker0。在宿主机ifconfig,就可以看到doccker0和自己create的network(后面讲)eth0，eth1，eth2……代表网卡一，网卡二，网卡三…… lo代表127.0.0.1，即localhost. inet addr用来表示网卡的IP地址

3网桥(交换机)docker0创建一对对等虚拟设备接口一个叫veth，另一个叫eth0，成对匹配。
	3.1整个宿主机的网桥模式都是docker0，类似一个交换机有一堆接口，每个接口叫veth，在本地主机和容器内分别创建一个虚拟接口，并让他们彼此联通（这样一对接口叫veth pair);
	3.2每个容器实例内部也有一块网卡，每个接口叫eth0;
	3.3 docker0上面的每个veth匹配某个容器实例内部的eth0，两两配对，一一匹配。
通过上述，将宿主机上的所有容器都连接到这个内部网络上，两个容器在同一个网络下,会从这个网关下各自拿到分配的ip，此时两个容器的网络是互通的。

![](img/Pasted%20image%2020240321083651.png)

#### host连接
和主机共享IP,共享端口,所以容易与主机端口冲突
甚至连在容器内ifconfig查看网络配置都是和主机一模一样的


### docker compose
就是将原有的多个镜像的dockerfiel写到一个docker-compose.yml里,然后指定谁依赖谁,启动的先后顺序

### 容器监控工具
cAdvisor收集服务浏览
influxdb存储服务
rafana展现服务
