

#### docker作用

>不同应用比如mysql,redis运行需要的各种依赖的库以及各种不同的配置参数,如果库版本不同会产生不兼容,所以每个容器中的应用都把依赖打包隔离不相互影响

不同操作系统,a程序依赖的系统函数的是centos,b程序依赖的系统函数用的是ubuntu,docker容器能把其所需要的操作系统函数也一起打包的容器中

##### docker主体使用思路
<font color=#99CCFF style=" font-weight:bold;">**docker就是一个小linux**</font>
你可以将你需要的镜像pull下来

然后运行这个这个镜像,运行的这个镜像称之为容器,可以给容器起名,还需要设置容器内端口和主机的映射端口.

为什么容器内有端口呢?因为docker创建的容器本身就是一个小linux,也是一个小文件系统

因此需要docker外的主机端口映射,这样主机某个端口被访问时,就会将信息转发给容器内进行端口映射的服务,

同时注意到云服务器上打开相应端口的安全组

同时容器本身是一个小文件系统,就如同linux一样

所以其也可以使用挂载,将运行的服务的需要频繁配置的文件挂载到主机的目录中,

这样就不用exec进入到docker容器中修改配置文件了

但注意,挂载到主机目录之前需要在docker内将文件复制到主机的挂载目录中

因为挂载到主机目录时容器内原来的配置文件会被主机的挂载目录覆盖,因此需要提前拷贝

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
##### 删除容器
```shell
docker rm 容器
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


#### 启动mysql举例
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
   ```c
   docker tag <本地镜像名称> <Docker Hub用户名>/<镜像名称>:<版本号>
   ```

4. 最后，使用 `docker push` 命令将镜像推送到Docker Hub上。使用以下命令来推送镜像：
   ```c
   docker push <Docker Hub用户名>/<镜像名称>:<版本号>
   ```

5. 执行完上述命令后，Docker会将镜像上传到Docker Hub上，并在你的个人仓库中可见。



### docker网络
1. **Bridge（桥接）**：<font color=#99CCFF style=" font-weight:bold;">默认的网络模式</font>，容器通过在同一主机上创建虚拟网桥连接到宿主机。容器可以使用IP地址访问其他容器或外部网络。
    
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

两种区别
![](img/Pasted%20image%2020240407203321.png)

##### 自定义网络
创建一个网络后,在这个网络的容器可以通信,
其他网络的容器想要与这个自定义网络里的所有容器通信,
可以直接连接这个自定义网络名,
所以很方便,
不用像其他网络那样管理容器网络很费事,
[详细](https://blog.csdn.net/CSDN1csdn1/article/details/123961079?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171246903816800211575644%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171246903816800211575644&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-123961079-null-null.142^v100^pc_search_result_base3&utm_term=docker%E7%BD%91%E7%BB%9C&spm=1018.2226.3001.4187)

##### none模式

加上后面的参数就行了 --net=none

none模式没有IP地址，无法连接外网，等于就是断网的状态，作用就是用于测试

##### container模式
这个模式指定新创建的容器和已经存在的一个容器共享一个 Network Namespace，而不是和宿主机共享。新创建的容器不会创建自己的网卡，配置自己的 IP，而是和一个指定的容器共享 IP、端口范围等。同样，两个容器除了网络方面，其他的如文件系统、进程列表等还是隔离的。两个容器的进程可以通过 lo 网卡设备通信


#### docker主机的路由表

```shell
[root@VM-4-10-centos ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.4.1        0.0.0.0         UG    0      0        0 eth0
10.0.4.0        0.0.0.0         255.255.252.0   U     0      0        0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U     1002   0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
```

- Destination：目标网络地址
- Gateway：<font color=#99CCFF style=" font-weight:bold;">路由数据包到达目标网络时要经过的下一网关</font>
- Genmask：子网掩码，用于确定网络中主机部分和网络部分的位数
- Flags：路由标志，如 UG 表示该路由是到一个默认网关（Default Gateway）的路由
- Metric：该路由的优先级，数值越小表示优先级越高
- Ref：路由的引用计数
- Use：显示该路由被使用的次数
- Iface：数据包在发送到目标网络时应该使用的网络接口

路由表中的每一条路由规则包含了目标地址、网关和网络接口这三个重要元素。当系统接收到一个数据包时，会根据目标地址查找匹配的路由规则，并确定数据包应该经过哪个网关以及使用哪个网络接口发送出去。这样确保了数据包能够正确到达目标网络。


##### 什么是网关?

Gateway（网关）：是数据包在到达目标网络之前经过的中间设备。当数据包要前往一个不在本地网络内的目标地址时，需要通过网关来实现路由转发。

<font color=#FFCCCC style=" font-weight:bold;">我们在建立主机网络时通常需要配置三个参数,ip地址,子网掩码,网关, 其中这个网关就是这个主机的默认路由,这个默认路由的地址是通往路由器的</font>


##### 什么是网卡(网络适配器)?

I数据包在传输过程中要经过的网络接口。每个网络接口都对应着系统中的一个物理或虚拟<font color=#99CCFF style=" font-weight:bold;">网卡</font>，。<font color=#FFCCCC style=" font-weight:bold;">网卡就是以面向对象的思想将网络接口抽象为一个对象</font>

##### 虚拟网卡 
(什么是vlan虚拟网络适配器)

docker0就是虚拟网络接口, 可以理解为别名网卡
一个主机上有一个网卡该网卡上有一个物理网络适配器,比如eth0
但还会有其他多个虚拟网络适配器, 比如一下情况:

1. **虚拟网络适配器**：系统会创建虚拟网络适配器，用于特定功能或网络配置。例如，VPN 连接、虚拟化软件（如 VMware、VirtualBox）创建的虚拟网络适配器还有docker都会显示为额外的网络适配器。
    
2. **无线网卡和有线网卡**：如果您的计算机同时连接了无线网络和有线网络，那么您会看到至少两个网络适配器：一个是无线网络适配器，另一个是有线网络适配器。
    
3. **隧道适配器**：某些软件可能会创建虚拟的隧道适配器，用于特定类型的网络通信，这样也会显示为一个额外的网络适配器。

**作用**

1. **网络连接**：虚拟网卡可以使虚拟机或容器等虚拟化环境实现与物理网络的连接，从而实现网络通信和数据传输。

2. **网络隔离**：通过虚拟网卡，可以在同一台主机上创建多个独立的网络环境，实现网络隔离，确保不同网络之间的安全性和独立性。

3. **网络虚拟化**：虚拟网卡可以为虚拟机提供独立的网络标识和配置，实现网络虚拟化，让虚拟机在虚拟网络中具有自己的网络特性。



##### 路由表分析
当前主机的路由表

```shell
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         主机设置的网关    0.0.0.0         UG    0      0        0 eth0
10.0.4.0     不用走路由器,局域网内 255.255.252.0   U     0      0        0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U     1002   0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
```

- default默认路由：所有不符合其他路由规则的目标IP地址将通过主机设置的网关进行转发。
    
- 10.0.4.0/22 子网：这个路由规则指示主机上的数据包不需要经过路由器，而是直接在局域网内传输。
    
- 169.254.0.0/16 子网：这个路由规则用于处理链接本地地址（Link-Local Address），用于主机与网络中直接相连的设备通信。Dhcp服务器坏了,分配不了ip, 就会用个子网的ip
    
- 172.17.0.0/16 子网：这个路由规则指示与Docker容器相关的数据包在docker0 接口上进行通信。

>网关为0.0.0.0就是当前局域网内,不需要走网关路由器,
网络适配器是eth0表示都是本主机内部的网络,
网络适配器是docker0表示走网桥docker0到另一个不同网段网络,所以这里的网桥相当于一个交换机,隔离了网络

##### 网络拓扑图
根据这个路由表，下面是基于本机的网络拓扑图：

```php
                                    +-----------+
                                    |           |
                                    | 主机配置的  |        
                                    | 网关路由器  |
                                    +-----+-----+
                                          |g0
                                          |
         +---------------------------------                   
         |                                               
   10.0.4.1 本机配置的
   默认网关,在本网络中的ip地址                                        
         |            +-----+------+--------+----------------------------------------              
         |            |    本机              |                          
         |            |10.0.4.10            |                      eth0
         |            |-----|------|--------+                   172.17.0.2
         |            | eth0|docker0        |       +-----------docker的redis容器
		 +------------|网络适|网桥(路由器不网段)|------|                       
					  | 配器 |172.17.0.0/16  |       +----------docker的nginx容器
				|10.0.4.0/22|                |		                172.17.0.3
                      +-----+----------------+                         eth0
                        |                    +--------------------------------------                  
                        |                                           
                        |                            
                   +----+----+                    
                   |         |                   
                 eth0       eth0                    
               10.0.4.3    10.0.4.2
```
<font color=#FFCCCC style=" font-weight:bold;">docker中的桥接模式本质使用的是nat,而不是虚拟机中的桥接模式,</font>
所以虚拟出来的容器ip是通过主机路由nat转发出去的,
因此和主机是不同网段,
虚拟机的桥接模式相当于和主机一个层级,直接连接在了主机的网关上

##### ifconfig查看网络适配器和交换机

```c
[root@VM-4-10-centos ~]# ifconfig 
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:3bff:fe11:6720  prefixlen 64  scopeid 0x20<link>
        ether 02:42:3b:11:67:20  txqueuelen 0  (Ethernet)
        RX packets 133702  bytes 133386772 (127.2 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 146889  bytes 20056160 (19.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.4.10  netmask 255.255.252.0  broadcast 10.0.7.255
        inet6 fe80::5054:ff:fe03:1576  prefixlen 64  scopeid 0x20<link>
        ether 52:54:00:03:15:76  txqueuelen 1000  (Ethernet)
        RX packets 211136408  bytes 28603346104 (26.6 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 213430320  bytes 27808515844 (25.8 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 15  bytes 1920 (1.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 15  bytes 1920 (1.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth8: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::c0a9:6dff:fe76:1c82  prefixlen 64  scopeid 0x20<link>
        ether c2:a9:6d:76:1c:82  txqueuelen 0  (Ethernet)
        RX packets 93996  bytes 109511147 (104.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 104132  bytes 16299650 (15.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

veth9: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::8852:dcff:fe7b:628  prefixlen 64  scopeid 0x20<link>
        ether 8a:52:dc:7b:06:28  txqueuelen 0  (Ethernet)
        RX packets 34111  bytes 24558983 (23.4 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 41544  bytes 3544672 (3.3 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```


##### 设置域名解析
```bash
vi /etc/hosts
```
修改配置文件
```shell
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2   os1       ##将另一个容器名字修改为os1
## 172.17.0.2   6d182f884ee1
```
ping域名
```bash
ping os1
```
### docker compose
就是将原有的多个镜像的dockerfiel写到一个docker-compose.yml里,然后指定谁依赖谁,启动的先后顺序

### 容器监控工具
cAdvisor收集服务浏览
influxdb存储服务
rafana展现服务
