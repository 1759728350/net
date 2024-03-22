##### dockerfile有哪些关键字？用途是什么
1. **FROM**：指定基础镜像，即新镜像是基于哪个镜像构建的
2. **RUN**：在<font color=#99CCFF style=" font-weight:bold;">镜像构建时</font>执行的命令，用于安装软件、配置环境等操作。可在当前层执行任何命令并创建一个新层，用于在映像层中添加功能层，后来的层会依赖它。
3. **COPY**：将文件或目录从构建上下文复制到镜像中。 
4. **EXPOSE**：声明容器运行时监听的端口，但并不真正发布该端口。
5. **VOLUME**：创建一个挂载点，用于持久化存储数据
6. ENV: 设置环境变量
7. CMD: 运行容器时执行的命令,docker run 后面的参数
8. ENTRYPOINT 指令为 docker exec 后面的参数

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


