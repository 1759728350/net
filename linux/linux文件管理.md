# 文件分析

#### ls命令及文件种类特点

来查看文件或目录的类型。`ls`命令以列表的形式显示文件和目录，并使用不同的标识符来表示它们的类型。以下是一些常见的文件类型标识符：

- `-` 表示普通文件。
- `d` 表示目录。
- `l` 表示符号链接（Symbolic Link）。
- `c` 表示字符设备文件。
- `b` 表示块设备文件。
- `s` 表示套接字（socket）文件。
- `p` 表示命名管道（named pipe）文件。


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


#### wc统计文件参数的命令

统计文件,文本的行数,字节数,单词数

可以通过管道符来对前一结果进行数据统计

```shell
who>test                        //相对于当前文件下的test
wc test

who|wc -l  ##统计登录的用户终端数
-l 统计前一个who的标准输出流中的行数
```



#### 文件分布

一个软件的文件分布在各个文件夹中

有python目录可以得出

```python
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

一个软件通常由多个组件或文件组成，它们可能包括可执行文件、配置文件、库文件、头文件、文档等。为了组织和管理这些文件，它们被放置在系统中不同的目录中。

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



# 文件权限
##### 文件目录权限更改

文件权限有: 

- **可读权限（read）（数值表示为 4）**：允许查看文件内容。
- **可写权限（write）（数值表示为 2）**：允许修改文件内容。
- **可执行权限（execute）（数值表示为 1）**：允许文件被运行。

一般来说，给一个脚本文件添加可执行权限是比较常见的需求。给用户、群组和其他用户分别赋予不同的权限可以通过以下命令实现：

```bash
chmod u+x a.sh   ##给该文件所有者加上可执行权限
## u所有者 g组 o其他人

##也可以这样写
chmod 744 a.sh 
## 读取（4）+写入（2）+执行（1）

chmod g+x script.sh  # 给所属群组添加可执行权限
chmod o+x script.sh  # 给其他用户添加可执行权限
chmod +x script.sh   # 给所有用户添加可执行权限

chmod -R 755 directory  ##使用421修改权限  -R递归修改
```

这些命令中的 `u` 表示文件所有者，`g` 表示所属群组，`o` 表示其他用户，`+x` 表示添加可执行权限。你可以根据具体需求来选择给哪些用户添加可执行权限。

例如，要给自己（所有者）、所属群组和其他用户同时添加可执行权限，可以使用以下命令：

```bash
chmod ugo+x script.sh
```

##### 更改所有者和所属组
```shell
修改所有者
chown user1 file.txt
修改所属组
chown :newgroup file.txt
```

总结: 

1. **查看文件权限**：使用 `ls -l` 命令查看文件的权限、所有者和所属组。
2. **修改文件权限**：使用 `chmod` 命令修改文件的权限，可以通过符号模式（如`u+r`）或数字模式（如`644`）来修改权限。
3. **修改文件所有者和所属组**：使用 `chown` 命令修改文件的所有者，使用 `chgrp` 命令修改文件的所属组。
4. **递归修改文件权限**：使用 `-R` 选项可以递归修改目录及其下所有文件的权限。
5. **精确控制文件权限**：了解权限位的含义，包括读、写、执行权限，以及文件所有者、所属组和其他用户的权限设置。




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

#### 更改创建文件touch

touch意味触摸的意思,更改一个已存在文件并更新其时间戳,如果文件不存在则创建文件

摸过一次就会创建时间戳

#### 用文件名查找文件

```python
find 路径 -name 文件名
##文件名可以用通配符

##从usr/lib路径下寻找以test结尾的文件
find /usr/lib -name *test

*表示空或任意多个的任意符号所以很好用
*aaa:以aaa结尾的文件
aaa*:以aaa为头的文件
*aaa*:文件名包含aaa的文件
```

linux中的所有命令相当于可执行文件,你可以这样理解:在linux中软件,二进制可执行文件,一行命令

这些都是软件,只不过由于windows这个图形化操作系统让我们误认为有界面的才是软件,其实一个命令也好,一个可执行程

序也好,都是软件

##### 输出当前路径pwd

```shell
pwd   ##print work directory打印当前目录路径
```

##### 路径规则

以/开头就是以根路径作为锚点

不以/开头就是以当前路径作为锚点 进行路径查找

```shell
./当前路径为锚点 ##take the current path as the source point |  take .. as把..当做   
../从上一路径为锚点## take the previous path as the source point

find ./ -name a.txt  ##find a.txt from the current directory
```




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

# 软件包管理及下载

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



















