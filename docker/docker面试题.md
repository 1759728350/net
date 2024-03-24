
##### 什么是 Docker 容器？

docker容器是方便打包分发的能在不同环境运行软件的应用程序
docker容器就是 Docker 镜像的运行后的实例。

##### docker和虚拟机有什么区别?
docker容器在应用层上做隔离,多个容器共享宿主机的系统内核
虚拟机每个都有一个系统内核

解释 Docker 组件。
三个架构组件包括 Docker 客户端、主机和注册表。

Docker 客户端：该组件执行构建和运行操作以与 Docker 主机通信。

Docker 主机：该组件包含 Docker 守护程序、Docker 镜像和 Docker 容器。守护进程建立到 Docker Registry 的连接。

Docker Registry：该组件存储 Docker 镜像。它可以是公共注册表，例如 Docker Hub 或 Docker Cloud，也可以是私有注册表。

##### dockerfile有哪些关键字？用途是什么
1. **FROM**：指定基础镜像，即新镜像是基于哪个镜像构建的
2. **RUN**：在<font color=#99CCFF style=" font-weight:bold;">镜像构建时</font>执行的命令，用于安装软件、配置环境等操作。可在当前层执行任何命令并创建一个新层，用于在映像层中添加功能层，后来的层会依赖它。
3. **COPY**：将文件或目录从构建上下文复制到镜像中。 
4. **EXPOSE**：声明容器运行时监听的端口，但并不真正发布该端口。
5. **VOLUME**：创建一个挂载点，用于持久化存储数据
6. ENV: 设置环境变量
7. CMD: 启动容器时执行的命令,可以做ENTRTPOINT指定命令的参数
8. ENTRYPOINT 启动容器时执行
9. LABEL: 打标签,用于自动化部署

##### 如何用Dockerfile构建镜像？

> $ docker build

##### 使用什么命令将新镜像推送到 Docker Registry？

> $ docker push myorg/img


##### 如何从 Docker 镜像创建 Docker 容器？

> $ docker run -it -d <image_name>

##### 如何列出所有正在运行的容器？

> $ docker ps

##### 如何减小dockerfile生成镜像体积
- 尽量选取满足需求但较小的基础系统镜像，例如大部分时候可以选择debian:wheezy或debian:jessie镜像，仅有不足百兆大小；
- 清理编译生成文件、安装包的缓存等临时文件；
- 安装各个软件时候要指定准确的版本号，并避免引入不需要的依赖；

##### dockerfile中COPY和ADD区别是什么？

COPY指令和ADD指令都可以将主机上的资源复制或加入到容器镜像中
如果是本地的压缩包ADD进去会被解压

##### docker网络类型有哪些？
host bridge none container

##### 命令相关：导入导出镜像，进入容器，设置重启容器策略，查看镜像环境变量，查看容器占用资源
- 导入镜像 docker load -i xx.tar
- 导出镜像docker save -o xx.tar image_name
- 进入容器docker exec -it 容器命令 /bin/bash
- 设置容器重启策略启动时 --restart选项
- 查看容器环境变量 docker exec {containerID} env
- 查看容器资源占用docker stats test2

##### 构建镜像有哪些方式？

- dockerfile
- 容器提交为镜像

##### docker compose怎么保证容器A先于容器B运行？
`depends_on` 关键字来指定容器之间的依赖关系，以确保某个容器在另一个容器之前启动。


##### 什么是Docker Swarm？

Docker Swarm 是一个容器编排工具，它允许我们跨不同主机管理多个容器。使用 Swarm，我们可以将多个 Docker 主机变成单个主机，以便于监控和管理。

