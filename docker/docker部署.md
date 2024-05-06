
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








### ssh密钥生成通过scp传输文件
可以使用Windows系统上的Git Bash命令行工具通过SSH协议将文件传输到一台运行Linux操作系统的云服务上。

1. **准备SSH密钥**：
   - 如果您还没有SSH密钥对，您可以使用以下命令在Git Bash中生成：

    ```shell
    ssh-keygen -t rsa -b 4096 -C "1759728350@qq.com"
    ```


密钥名防止覆盖
```shell
$ ssh-keygen -t rsa -b 4096 -C "1759728350@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/HUAWEI/.ssh/id_rsa): /c/Users/HUAWEI/.ssh/id_rsa2
```

   - 按照提示，选择密钥保存的位置和设置密码。这将生成一个私钥文件（通常为 `id_rsa`）和一个公钥文件（通常为 `id_rsa.pub`）。

2. **将公钥添加到云服务的authorized_keys文件**：
   - 将生成的公钥内容添加到您云服务上SSH用户的 `~/.ssh/authorized_keys` 文件中。您可以使用`scp`命令或其他文件传输方式将公钥内容复制到云服务上。

3. **使用scp命令传输文件**：
   - 在Git Bash中，使用`scp`命令将文件从本地传输到云服务上。示例命令如下：

```bash
scp /c/Users/HUAWEI/Desktop/项目.zip root@110.42.246.104:/testdir
```

    - 将 `/path/to/local/file` 替换为您本地文件的路径。
    - 将 `username` 替换为您在云服务上的用户名。
    - 将 `hostname` 替换为云服务的主机名或IP地址。
    - 将 `/path/to/remote/directory` 替换为您希望将文件传输到的远程目录。

4. **输入SSH密码**：
   - 当您运行上述`scp`命令时，Git Bash会提示您输入SSH密码。请输入您在云服务上的SSH密码，然后按Enter键。

完成

注意开启云的防火墙22端口


    


### 部署lol静态页面

**在当前主机上成密钥**
```shell
ssh-keygen -t rsa -b 4096 -C "1759728350@qq.com"
```

将公钥放入服务器中, 然后可以远程登录并对传输文件给云服务器
```shell
vim ~/.ssh/authorized_keys   ##然后将公钥粘贴进去
```

**使用协议将文件传输到云服务器上**
```shell
scp lol-project.zip root@你的云服务器ip:/myproject
```

**对文件编码进行更改以及解压**
```shell
unzip -O GBK lol-project.zip
```


**编写Dockerfile**
编写dockerfile将文件构建为镜像, 因为是简单的静态页面所以就用nginx作为服务器

注意编码格式, windows默认使用GBK对文件进行编码,linux默认使用UTF-8
```Dockerfile
FROM nginx:latest  
ENV LANG=zh_CN.UTF-8


COPY . /usr/share/nginx/html
```


**构建Docker镜像**：
	进入Dockerfile所在的目录

    ```shell
    docker build -t lol_img .
    ```

**查看构建的镜像**：
```shell
 docker images
```

```shell
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
lol-img      latest    ea4661c4b0d4   26 minutes ago   197MB
tomcat       latest    549891b60da9   2 months ago     455MB
redis        latest    d1397258b209   3 months ago     138MB
nginx        latest    61395b4c586d   7 months ago     187MB
centos       centos7   eeb6ee3f44bd   2 years ago      204MB   
```

部署脚本
```shell
#!/bin/bash
unzip -O GBK lol-project.zip

docker ps -a
docker stop hedley_lol
docker rm hedley_lol
docker rmi lol-img:latest
docker build -t lol_img .
docker run -d -p 9906:80 --name hedley_lol lol_img
docker ps -a

```

**运行容器：**
   执行以下命令来运行刚刚构建的镜像：

   ```shell
   docker run -d -p 9906:80 --name hedley_lol lol-img
   ```

   这会在端口80上运行Nginx容器，并将容器的80端口映射到主机的9906端口。

**查看容器是否运行成功**

```shell
docker ps -a
```
```shell
CONTAINER ID   IMAGE            COMMAND                   CREATED          STATUS          PORTS                                       NAMES
8c0cb6a79b3f   lol-img          "/docker-entrypoint.…"   25 minutes ago   Up 25 minutes   0.0.0.0:9906->80/tcp, :::9906->80/tcp       hedley_lol
3f21f7549bc6   centos:centos7   "/usr/sbin/init"          2 weeks ago      Up 2 weeks      0.0.0.0:60002->22/tcp, :::60002->22/tcp     hedley_centos_2
```


**访问网站：**
   打开浏览器，并访问http://你的ip:9906你应该能够看到nginx页面
如果目录是
```
lol-project/作业2-前端仿站/index.html
```
那么路径就是
```shell
http://110.42.246.104:9906/lol-project/作业2-前端仿站/index.html
```


### 部署python项目
使用Docker部署Python项目相当简单，下面是一个基本的步骤：

1. **编写 Dockerfile**：在你的项目根目录中创建一个名为 `Dockerfile` 的文件，并按照以下方式编写：

```Dockerfile
# 使用 Python 官方镜像作为基础镜像
FROM python:3.8
FROM nginx:latest

ENV LANG=zh_CN.UTF-8
# 设置工作目录
WORKDIR /app

# 将当前目录中的所有文件复制到容器的 /app 目录中
COPY . /app
COPY . /usr/share/nginx/html

# 设置 pip 使用清华大学的源 
RUN pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# 安装项目所需的依赖
RUN pip install --no-cache-dir -r requirements.txt

# 在容器内运行的命令
CMD ["python", "main.py"]

```

```shell
FROM python:3.8
FROM nginx:latest

#RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list && \
#    sed -i 's/security.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list

#RUN apt-get update && apt-get install -y python3-pip

ENV LANG=zh_CN.UTF-8

WORKDIR /app

COPY . /app
COPY . /usr/share/nginx/html

RUN pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

RUN pip install --no-cache-dir -r requirements.txt

```


##### 容器挂掉原因
容器挂掉的原因通常可以通过查看容器的日志来确定。在你的情况下，你的容器运行了一个名为 `app.py` 的 Python 应用，但是很快就退出了。

你可以通过以下步骤来分析容器挂掉的原因：

1. **查看容器日志：** 使用 `docker logs <container_id>` 命令来查看容器的日志，其中 `<container_id>` 是你容器的 ID。这将显示容器的标准输出和标准错误输出，这些输出通常包含导致容器退出的错误消息。

2. **检查应用程序代码：** 如果容器的日志没有提供足够的信息，你可能需要检查应用程序代码，特别是 `app.py` 文件，看看是否有任何明显的错误。

3. **检查容器的环境变量和启动命令：** 确保容器的环境变量设置正确，并且启动命令与你预期的相符。在你的情况下，确保 `app.py` 的启动命令正确。

4. **检查容器的依赖项：** 如果你的应用程序依赖于其他服务或软件，确保这些服务或软件在容器启动时可用，并且容器能够访问它们。

5. **查看容器的健康状态：** 使用 `docker inspect <container_id>` 命令来查看容器的详细信息，包括健康状态。这可能会提供有关容器健康状况的更多信息。

6. **尝试手动运行应用程序：** 如果可能的话，尝试在容器外部手动运行应用程序，看看是否会出现相同的问题。这有助于确定问题是在容器环境中还是应用程序本身中。

通过这些步骤，你应该能够确定造成容器挂掉的原因，并采取相应的措施来解决问题。如果你需要进一步的帮助，请提供容器日志或其他相关信息，我将竭诚为你服务。

##### 部署脚本


```shell
#!/bin/bash
unzip -O GBK project.zip

docker stop liziyu_stock 
docker rm liziyu_stock
docker rmi liziyu_img:latest 
docker build -t liziyu_img .
docker images
docker run -d -p 9907:80 --name liziyu_stock liziyu_img

docker ps -a

```


```shell
chmod +x a.sh

./a.sh
```



##### 部署2
好的，我们来逐步进行：

1. **创建 Dockerfile**：

   在你的 Flask 应用程序根目录创建一个 Dockerfile 文件。以下是一个基本示例：

   ```Dockerfile
   # 使用官方的 Python 运行时作为基础镜像
   FROM python:3.9-slim

   # 设置容器中的工作目录
   WORKDIR /app

   # 将当前目录内容复制到容器中的 /app 目录
   COPY . /app

   # 安装 requirements.txt 中指定的所有依赖项
   RUN pip install --no-cache-dir -r requirements.txt
   ```

2. **编写 Docker Compose 文件**（可选）：

   如果你想要使用 Docker Compose 管理多个容器，可以创建一个 `docker-compose.yml` 文件。示例：

   ```yaml
   version: '3'
   services:
     web:
       build: .
       ports:
         - "5000:5000"
   ```

3. **创建 Nginx 配置文件**：

   创建一个 Nginx 配置文件来配置反向代理以将请求转发到 Flask 应用程序。示例：

   ```nginx
   server {
       listen 80;
       server_name your_domain.com;

       location / {
           proxy_pass http://web:5000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

4. **创建 Docker 容器**：

   在终端中，导航到你的应用程序目录，并运行以下命令来构建和运行容器：

   ```bash
   docker build -t my-flask-app .
   docker run -d -p 5000:5000 my-flask-app
   ```

   如果你使用了 Docker Compose，可以运行以下命令来启动服务：

   ```bash
   docker-compose up -d
   ```

这样，你的 Flask 应用程序就会在 Docker 容器中运行，并通过 Nginx 进行反向代理。