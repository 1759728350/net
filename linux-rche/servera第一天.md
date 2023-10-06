
系统评分需要重启的

##### 配置自动联网
进电脑配置自动联网
![[Pasted image 20231005221011.png]]

修改系统ip,dns,网关,子网掩码
```shell
cd /etc/sysconfig/network-scripts/ifcfg  下修改
```

##### 主机ssh登录node1
登录失败说明ip没配对,是一种验证方式
```shell
ssh root@172.25.250.100
```


##### 修改主机名
```shell
先远程登录到node1后
hostnamectl set-hostname [主机名]
刷新一下当前命令行,看看名字有没有改变
exec bash
重启一下看看有没有修改成功,过几分钟node1启动后再远程登录
reboot 

```

##### 配置存储库
```shell
vim /etc/yum.repos.d/rhel.repo

##vim中修改
[a]
name=aa
baseurl=看
gpgcheck=0
enabled=1

[b]
name=bb
baseurl=看
gpgcheck=0
enabled=1
```

##### 调试selinux
什么是selinux?
linux的安全模块,对于端口,文件定义哪些进程可以执行操作,哪些端口,文件可以被访问
![[Pasted image 20231006002349.png]]
意思就是我们要给web服务器的http服务开启82端口的端口转发服务
```shell
semanage port -l | grep http
##输出http_port_t                    tcp      80, 81, 443, 488, 8008,
semanage port -a -t http_port_t -p tcp 82
-a添加,-t类型,-p协议 最后一个要转发的端口号
```
做到这别的主机任然访问不了,还需要开启node1的防火墙
```
允许其他主机通过tcp协议通过该机的82号端口与该机通信
firewall-cmd --add-port=82/tcp              ## 当次生效
firewall-cmd --add-port=82/tcp --permanent  ## 下次启动生效,但当次不生效
```
接下来我们看/var/www/html下有哪些要访问的文件
root@node1 ~]# cd /var/www/html
root@node1 html]# ll -Z
total 12
-rw-r--r--. 1 root root system_u:object_r:default_t:s0                   14 Oct  5 13:32 file1
-rw-r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0 14 Oct  5 13:32 file2
-rw-r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0 14 Oct  5 13:32 file3
![[Pasted image 20231006004643.png]]
对于访问不到的文件我们需要修改标签
```shell
##永久更改
##semanage fcontext -a -t httpd_sys_content_t file1  ##复制标签是不要复制s0

##上面那个不行优先这个
chcon -t 标签 file1

##刷新文件标签
restorecon file1
```
设置开机启动web服务器
```shell

最后才启动httpd服务,如果提前启动了需要stop然后start
##启动web服务
systemctl start httpd

##开机自启
systemctl enable httpd
```

##### 创建用户
创建一个用户组
```shell
groupadd 组名
```
创建一个在某用户组下的用户(某用户次要组从属于该组)
```
##-G指定组名
useradd -G 组名 用户名
```
创建一个用户时指定其shell,非交互式shell是/bin/false
```
useradd -s /bin/false 用户名
```
设置用户密码
```
echo 密码 | passwd --stdin 用户名
echo redhat | passwd --stdin nata
```


##### 创建cron循环任务
```shell
编辑对应用户的cron配置,这个任务以该用户身份每个周期会运行
crontab -e -u natasha
##分钟,小时,日,月,周    在文件中修改
*/2 * * * 命令
```

##### 协作目录
更改目录的群组,使得只有某组的用户才能访问
```shell
mkdir /home/managers
chgrp 群组 目录

##ll -d 目录 查看目录本身的信息,包括改目录所有者,该目录所属群组
[root@node1 html]# ll -d /home/managers/
drwxr-xr-x. 2 root sysmgrs 6 Oct  6 12:11 /home/managers/
```

