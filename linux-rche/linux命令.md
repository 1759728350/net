# linux文件管理

#### ll命令文件分析

```shell
drwxr-xr-x 2 root root 4096 10月 15 17:40 testdir_backup
```

- `d`：以字母 "d" 开头表示这是一个目录,一个杠代表文件
- `rwxr-xr-x`：(所有者、所属组、其他用户）的权限 ,u,g,o
- `2`：这个目录中的项目数目（包括子目录）。
- `root`：所有者的用户名。
- `root`：所属组的组名。
- `4096`：目录的大小（以字节为单位）。
- `10月 15 17:40`：最后一次修改该目录的日期和时间。
- `testdir_backup`：目录的名称

#### 文件目录权限更改

chmod命令

```shell
chmod u+x a.sh   ##给该文件所有者加上可执行权限
## u所有者 g组 o其他人

##也可以这样写
chmod 744 a.sh 
## 读取（4）+写入（2）+执行（1）
```

#### 文件分布

一个软件的文件分布在各个文件夹中

有python目录可以得出

```shell
whereis $(readlink -f $(which python))
python2: /usr/bin/python2 /usr/bin/python2.7-config /usr/bin/python2.7 /usr/lib/python2.7 /usr/lib64/python2.7 /usr/include/python2.7 /usr/share/man/man1/python2.1.gz
```

意味着在您的系统中，`python2`命令的相关文件可以在以下位置找到：

- `/usr/bin/python2`: `python2`的可执行文件路径
- `/usr/bin/python2.7-config`: `python2.7-config`的可执行文件路径
- `/usr/bin/python2.7`: `python2.7`的可执行文件路径
- `/usr/lib/python2.7`: `python2.7`的库文件路径
- `/usr/lib64/python2.7`: `python2.7`的64位版本库文件路径
- `/usr/include/python2.7`: `python2.7`的头文件路径
- `/usr/share/man/man1/python2.1.gz`: `python2`的手册页路径

#### 常用目录特点

是的，这在Linux系统中很常见。一个软件通常由多个组件或文件组成，它们可能包括可执行文件、配置文件、库文件、头文件、文档等。为了组织和管理这些文件，它们被放置在系统中不同的目录中。

在Linux中，常见的目录结构如下：

- `/bin`: 存放系统的基本命令，可以直接在命令行中执行的可执行文件。
- `/sbin`: 存放系统管理员使用的命令，一般需要超级用户权限才能执行的可执行文件。
- `/usr/bin`: 存放用户安装的软件的可执行文件。
- `/usr/sbin`: 存放用户安装的软件的管理员命令的可执行文件。
- `/lib`: 存放系统所需的共享库文件。
- `/usr/lib`: 存放用户安装的软件所需的共享库文件。
- `/include`: 存放C/C++头文件。
- `/usr/include`: 存放用户安装的软件所需的C/C++头文件。
- `/usr/share/man`: 存放命令的手册页。
- `/etc`: 存放系统的配置文件。
- `/var`: 存放变量数据，例如日志文件、缓存文件等。

每个目录都有其特定的用途，通过将软件的不同组件放置在合适的目录中，可以更好地管理和组织软件文件，并使系统能够正确地找到和使用这些文件。

44

# linux基本命令

linux中的所有命令相当于可执行文件,你可以这样理解:在linux中软件,二进制可执行文件,一行命令

这些都是软件,只不过由于windows这个图形化操作系统让我们误认为有界面的才是软件,其实一个命令也好,一个可执行程

序也好,都是软件

#### 输出当前路径pwd

```shell
pwd   ##print work directory打印当前目录路径
```

#### 路径规则

以/开头就是以根路径作为锚点

不以/开头就是以当前路径作为锚点 进行路径查找

```shell
./当前路径为锚点 ##take the current path as the source point |  take .. as把..当做   
../从上一路径为锚点## take the previous path as the source point

find ./ -name a.txt  ##find a.txt from the current directory
```



#### 更改创建文件touch

touch意味触摸的意思,更改一个已存在文件并更新其时间戳,如果文件不存在则创建文件

摸过一次就会创建时间戳

#### 用文件名查找文件

```shell
find 路径 -name 文件名
##文件名可以用通配符

##从usr/lib路径下寻找以test结尾的文件
find /usr/lib -name *test

*表示空或任意多个的任意符号所以很好用
*aaa:以aaa结尾的文件
aaa*:以aaa为头的文件
*aaa*:文件名包含aaa的文件
```



#### ls命令及文件种类特点

来查看文件或目录的类型。`ls`命令以列表的形式显示文件和目录，并使用不同的标识符来表示它们的类型。以下是一些常见的文件类型标识符：

- `-` 表示普通文件。
- `d` 表示目录。
- `l` 表示符号链接（Symbolic Link）。
- `c` 表示字符设备文件。
- `b` 表示块设备文件。
- `s` 表示套接字（socket）文件。
- `p` 表示命名管道（named pipe）文件。

# 文本编辑
#### vim

* 命令行模式

按esc进入命令行模式,该模式可以通过按键代替鼠标移动光标,进行移动光标操作

命令行模式的快捷按键:

hjkl移动光标  0移到行首 shift+4移动到行尾

/查找,光标到下面时输入你要找的值,和ctrl+f一个效果     

然后n是跳到下一个结果,shift+n跳到上一个匹配成功的结果

* 插入模式

i和一些其他键,进入写入模式

* 终结模式

按:   进入然后参数w保存,参数q退出



其他命令行模式命令

d删除

u相当于ctrl+z

r相当于ctrl+y反向撤销,和ctrl+z相反

gg跳到第一行,shift+g跳到最后一行

v选择块,然后移动光标

y复制,p粘贴

#### grep过滤

查找过滤一个文件/文本含有对应匹配条件的内容

当我们对一批文件进行了操作但只想显示目的文件,我们就可以在命令后面加管道符| grep

#### 管道符

管道符会将前面的终端输出视作一个文本文件

管道后面的命令会将这个终端输出的所有字符当做一个文件去使用

#### 命令解析占位符

管道符的逆向,一些无法读取标准输入(控制台输入的文字)的命令想用另外一个命令的结果当做参数

此时就得用

```shell
readlink -f $(which python)
``也可以代替$()
##which命令   输出某个命令的可执行文件的地址,只包含可执行文件的地址
## 与之相比
##whereis命令 输出某个命令相关的所有文件路径,包括源代码,可执行文件,手册

whereis $(readlink -f $(which python))
python2: /usr/bin/python2 /usr/bin/python2.7-config /usr/bin/python2.7 /usr/lib/python2.7 /usr/lib64/python2.7 /usr/include/python2.7 /usr/share/man/man1/python2.1.gz 
##因为python本身是环境变量,所以which python获得python这个命令的路径
##发现这个路径是软连接,所以用readlink -f来递归找到其文件路径
lrwxrwxrwx  1 root root           7 3月  23 2023 python -> python2
lrwxrwxrwx  1 root root           9 3月  23 2023 python2 -> python2.7
-rwxr-xr-x  1 root root        7144 6月  28 2022 python2.7
##一路递归最终找到python这个软连接指向的python2.7  -rwxr说明这是个文件,l开头表示软连接
##whereis python2.7就能获取该文件的相关所有源代码,可执行文件,手册
```

# 文件操作

#### 复制文件

```shell
cp  /test/test.txt /test/test2.txt  #把text文件复制一份,名字叫test2
cp -r ##递归复制
cp -r /testdir /home/arr   ##复制到home文件夹下,名字叫arr
## 后面复制后生成的文件夹,所以不能cp -r /test / ,这相当于把根目录给覆盖了
```

#### 移动文件

```shell
mv移动命令

mv 要移动的文件文件夹 被移动后生成的文件夹文件
##移动文件夹不需要加r
```

#### 删除文件

```shell
rm删除目录

rm a.txt
rm -rf testdir ##递归删除文件,-f不询问,否则文件夹中的每个文件都会询问一遍
```



#### wc统计文件参数的命令

统计文件,文本的行数,字节数,单词数

可以通过管道符来对前一结果进行数据统计

```shell
who>test                        //相对于当前文件下的test
wc test

who|wc -l  ##统计登录的用户终端数
-l 统计前一个who的标准输出流中的行数
```

#### 查看使用历史命令

```shell
history
313  2023-10-15 20:48:42 ./ash.sh sjdksad
314  2023-10-15 20:48:50 vim ash.sh 
315  2023-10-15 20:02:11 export LANG="en_US";export LANGUAGE="en_US";export LC_ALL="en_US";top

!313  ##感叹号 历史命令id  快速复用命令  最多记录3000个
```



#### 终端输出重定向符

```shell
> 一个是覆盖到对应文件
>> 两个表示追加到文件

ll -a -l > b.txt
```

echo命令可以将字符串输出到终端,也就是说echo可以将一个字符串转化为一个文件单位

而重定向符号和管道都可以利用终端上的内容

```shell
echo hello >> ./testdir/b.txt
```

#### 跟踪文件实时写入

输出文件最后几行 

```shell
tail -5 -f a.txt  ##-f means to follow,to listen for the changes to this file

tail -3 c.txt| tail -1  ##take the last third line as the text and read the last line
```



#### 终止当前终端

ctrl+c终止当前终端的程序执行

ctrl+d退出当前应用的命令行,回到终端

退出当前运行的终端,但不停止程序
```shell
exit   
```

#### 软件包管理及下载

>得先区分linux的发行版
>debian和Ubuntu是apt-get
>red hat和centos/fedora是yum

```shell
##查看是否已经安装,当然也可以使用--version
sudo apt-cache policy vim
##安装vim
sudo apt-get install vim
##配置vim
vim /etc/vim/vimrc
##卸载vim
sudo apt-get remove vim
```
```shell
查找不到,apt-get:找不到命令,就是用的centos
yum update
yum install -y vim
```

##### 软件打包压缩
```shell
##将多个文件或目录打包成一个文件，但不进行压缩。因此，使用`tar`打包后的文件大小比较大，但打包速度快。通常需要结合其他压缩工具（如`gzip`
tar -czvf archive.tar.gz /path/to/directory  #z表示使用gzip进行压缩
tar -xzvf  ##表示将压缩文件进行解压

zip
upzip  ##解压

gzip   ##对单个文件进行压缩
```
# linux部署
##### 开启防火墙对应端口
```shell
firewall-cmd --zone=public --add-port=3306/tcp -permanent
firewall-cmd -reload
```

##### 查看服务运行状态
```shell
systemctl status mysqld
```
# linux磁盘管理

* 磁盘格式化

* 先给系统添加磁盘

* 给磁盘分区,磁盘是sdb,分区是后面加数字的sdb2

```shell
lsblk -f ##查看本机所有分区的文件系统
```

* 给分区格式化文件系统

* 将该文件系统挂载到系统目录

```shell
mount /dev/sdb2 /mnt  ##将设备文件夹下的sdb分区挂载到/mnt下,sdb2已经创建文件系统

lsblk -f
NAME   FSTYPE  LABEL    UUID                                 MOUNTPOINT
sr0    iso9660 config-2 2023-09-25-19-18-04-00               
vda                                                          
└─vda1 ext4             4b499d76-769a-40a0-93dc-4a31a59add28 /

mount -l | grep vd 
/dev/vda1 on / type ext4 (rw,relatime,data=ordered)
##我们可以看到这个腾讯云只有一个块设备磁盘vda,这个磁盘只有一个分区vda1
## 这个分区的文件系统是ext4,他挂载到了本文件系统的/根目录下

```

#### 挂载理解

我们不能把文件系统和磁盘搞混了

各种linux内核,linux启动boot引导程序,用户个人文件都是需要挂载到linux的目录上的

上面那些都本身就是个文件系统,挂载到了linux上的文件系统上的目录中

也就是说linux本身就有一个文件系统

后来的各种日志系统,临时文件系统,块设备,远程服务器设备,都通过挂载的方式连接到linux文件系统上

只不过像添加一块硬盘

你需要对硬盘分区

然后对硬盘的分区装上文件系统

然后再把该文件系统格式化到linux的文件系统的目录中



远程服务器也是同理

先要格式化一个文件系统,你才能被其他linux主机连接挂载到其文件系统中

#### 虚拟文件系统

像proc,sysfs这些运行在内存中的文件系统,其本身不占用磁盘空间

因此你使用df -HT是查不到其文件系统占用的磁盘空间的

```shell
[root@VM-4-10-centos testdir]# df -HT
文件系统       类型      容量  已用  可用 已用% 挂载点
devtmpfs       devtmpfs  1.1G     0  1.1G    0% /dev
tmpfs          tmpfs     1.1G   25k  1.1G    1% /dev/shm
tmpfs          tmpfs     1.1G  558k  1.1G    1% /run
tmpfs          tmpfs     1.1G     0  1.1G    0% /sys/fs/cgroup
/dev/vda1      ext4       53G  5.3G   46G   11% /
```

#### swap分区

啥时候需要用到swap分区呢?

当系统内存不足.程序要内存溢出了,或者应用程序需要特别多的内存,或面对内存密集型任务时,我们可以

开启swap分区,将不怎么用到的内存页交换到磁盘上

# linux状态监控

#### 进程监控

```shell
ps -f ##查看主要运行的进程
ps -ef  ##查看所有进程,一般用grep过滤

[root@VM-4-10-centos ~]# bash 
[root@VM-4-10-centos ~]# sh
sh-4.2# ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
root      9135 30099  0 09:05 pts/0    00:00:00 bash
root      9457  9135  0 09:05 pts/0    00:00:00 sh
root      9547  9457  0 09:05 pts/0    00:00:00 ps -f
root     30099 30090  0 08:56 pts/0    00:00:00 -bash

##先进入bash 再进入sh,就会看到这两个程序的进程
```

```shell
pstree    ##查看进程树
```




# 云原生

使用docker

#### 运行容器

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



#### 多服务器服务

同时docker还可以将服务分发部署到其他容器中

比如一台主机中的多个容器运行多个nginx

或者私有网络中每个主机都会运行一个nginx的容器服务

我们面对的是如何将这些我们已经配置好的容器给其他机器

这时候有两种方法

* 压缩成文件后拷贝过去
* 提交的dockerhub中去

第一种方法可以将镜像打成tar包,然后到其他机器上解压就行了

第二种方法需要将镜像push到网站上,然后到其他机器上pull下来



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



# rche考试题







下班了吗?

哦下了下了,刚下班

* 您上班也累啊,患者不停的,忙的时候可能连喝水都没时间

偶尔,不会那么忙

* 多注意休息休息,别累坏了

不算累,谢谢哦



* 国庆患者还是很多吧

  

* 现在咱们这代压力都还是不小的
* 我感觉我那个专业有一堆学不完的东西
* 医学坑定学得更多

学得确实多

* 你牙科也学哪些临床要学的科目吗

是的





