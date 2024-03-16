
##### linux启动过程
1. 启动bios,检测硬件
2. bootloader使用grub来引导加载内核
3. 启动内核kernel,加载文件系统
4. Systemd 通过每个服务的单元文件来启动服务,包括网络服务、日志服务
5. 进入多用户模式target,再进入登录界面


##### 创建目录写入文件 
创建目录/data/laoxin,并且在该目录下创建文件laoxin.txt,然后在文件“laoxin.txt”里写入内容“inet addr : 10.0.0.8 Bcast : 10.0.0.255 Mask : 255.255.255.0
```shell
```bash
mkdir -p /data/laoxin
```

2. 在`/data/laoxin`目录下创建文件`laoxin.txt`并写入内容：

```bash
echo "inet addr : 10.0.0.8 Bcast : 10.0.0.255 Mask : 255.255.255.0" > /data/laoxin/laoxin.txt
```

##### linux文件类型
linux中有哪些文件类型
|ls -l可以显示
>- 常规文件,d目录文件,b比如硬盘的块设备文件,l链接文件,p管道文件,s套接字文件

##### **Linux下软链接和硬链接的区别**
1. 删除源文件软连接会失效,但硬链接不会,所以想彻底删除就需要将硬链接一起删除
	创建软链接就是ln -s 硬链接就ln

2. 硬链接的文件与源文件具有相同的inode节点号，它们实际上指向同一个数据块，即同一个文件内容。而软链接的文件与源文件具有不同的inode节点号，软链接文件中存储的是源文件的路径信息，通过该路径找到源文件。
    
3. `ln`命令无法对目录创建硬链接，因为硬链接是基于文件系统的inode节点，目录本身也是一种特殊的文件，不可以直接创建硬链接。但是，可以通过软链接将目录链接到另一个位置，这样可以方便地访问目录。
4. 软链接可以跨文件系统创建链接

##### centos6和centos7的区别
1. **系统初始化：**
    
    - CentOS 6使用`init`初始化系统，而CentOS 7使用`systemd`作为初始化系统。
2. **防火墙管理：**
    
    - CentOS 6中默认使用`iptables`作为防火墙管理工具，而CentOS 7中默认使用`firewalld`。
3. **服务管理：**
    
    - CentOS 6使用`service`和`chkconfig`命令管理服务，而CentOS 7使用`systemctl`命令管理服务。
4. **网络配置：**
    
    - CentOS 6中网络配置文件位于`/etc/sysconfig/network-scripts/`目录下，而CentOS 7中使用`NetworkManager`进行网络配置。
5. **文件系统：**
    - CentOS 6默认使用`ext4`文件系统，而CentOS 7默认使用`XFS`文件系统。

##### centos和ubuntu的区别
1. **软件包管理：**
    
    - CentOS使用`yum`（或最新版本的`dnf`）作为软件包管理工具，而Ubuntu使用`apt`（Advanced Package Tool）或`apt-get`。这两种工具在命令和用法上有一些不同，因此需要根据系统选择合适的软件包管理工具。
2. **发行版本周期和稳定性：**
    
    - CentOS是基于Red Hat Enterprise Linux（RHEL）构建的开源发行版，注重稳定性和长期支持（Long Term Support，LTS），更新速度较慢。而Ubuntu有定期发布的时间表，提供标准支持版本和长期支持版本，更新速度会稍快一些。
3. **社区和支持：**
    
    - Ubuntu拥有更广泛的用户社区和支持资源，因为它是一个非常受欢迎的桌面和服务器操作系统。CentOS虽然也有相当大的用户群体，但受众相对较少，社区支持可能相对较少一些。

