
### 应用到磁盘的层级结构
在数据从磁盘到应用程序的过程中，涉及了以下几个层次：

1. 磁盘：数据最初存储在磁盘上，磁盘是数据的物理存储介质。
    
2. 驱动程序（driver）：为了访问磁盘上的数据，操作系统需要相应的驱动程序来控制磁盘，读取和写入数据。驱动程序负责与硬件设备进行通信。
    
3. 文件系统：文件系统管理磁盘上的数据组织和存储。它负责文件的创建、删除、读取和写入，以及对数据的组织和存储。
    
4. 虚拟文件系统（Virtual File System，VFS）：VFS是操作系统内核中的一个抽象层，它提供了一个统一的接口，使应用程序可以通过相同的方式访问不同类型的文件系统，如FAT、NTFS、EXT等。VFS隐藏了底层文件系统的差异，使应用程序可以透明地访问不同文件系统。
    
5. 应用程序层：最终数据被应用程序读取、处理和展示给用户。应用程序通过VFS接口调用文件系统来访问磁盘上的数据，完成特定的功能。




## 磁盘
##### 磁盘类型

机械和固态
一般阿里云采用固态,读写快,耗电量低

### 磁盘阵列
**什么是磁盘阵列**
磁盘阵列是将多个磁盘组合在一起的存储系统,
而RAID技术就是探讨利用什么样的算法来实现这个存储系统,怎么组合多个磁盘?怎么读写

RAID技术在数据中心、服务器和企业存储环境中被广泛使用，以提高数据可靠性、性能和效率。
##### 磁盘组合技术(RAID)
>RAID（冗余阵列独立磁盘）<font color=#99CCFF style=" font-weight:bold;">是一种通过将多个磁盘组合在一起来提高数据存储性能、容量和/或冗余度的技术。</font>

以下是几种常见的RAID级别：

1. **RAID 0**：并发分散存储在多个磁盘内,读写速度快,但是不做冗余备份,一旦一个磁盘坏了,数据就没了

2. **RAID 1**：把数据同时写入两个盘,另一个做备份,有镜像备份,但是浪费空间

3. **RAID 5**：将数据和其奇偶校验信息并发写入多个盘,一个盘如果坏了,还可以通过其他盘恢复

4. **RAID 10**：RAID 10结合了RAID 1和RAID 0的特点,既做备份又做并发存储,所以更浪费空间

根据读写性能,数据安全性, 存储成本 来选择

##### 最常用级别
一般情况下，默认的RAID级别取决于具体的硬件设备和系统配置。在许多情况下，RAID 1或RAID 5是较为常见的默认选择。

- **RAID 1**：最简单的保证数据安全, 适用于对数据安全性要求较高的场景，如企业数据库、关键应用程序等。
  
- **RAID 5**：升级版的raid0,有一点安全性,也能读写快

##### 适用场景

1. **RAID 0**：
   - **场景**：需要提高性能而对数据冗余性要求不高的场景。
   - **适用情况**：图像/视频编辑、科学计算、临时数据存储等需要快速读写速度而无需数据冗余备份的场景。

2. **RAID 1**：
   - **场景**：数据安全性和冗余备份是首要考虑因素的场景。
   - **适用情况**：关键业务数据、数据库服务器、企业应用程序等对数据可靠性要求较高的场景。

3. **RAID 5**：
   - **场景**：寻求平衡性能和冗余备份的场景。
   - **适用情况**：中小型企业存储、文件服务器、虚拟化环境等需要较好性能和一定冗余备份的场景。

4. **RAID 10**：
   - **场景**：同时追求性能和冗余备份的场景,但花费最高。
   - **适用情况**：数据库服务器、虚拟化环境、企业级应用程序等需要高性能和高冗余性的关键业务场景。

##### 设置级别

1. **在安装操作系统时设置RAID级别**：
   - 在安装过程中，通常会有设置磁盘和RAID选项的步骤。
   - 用户可以在这些选项中选择所需的RAID级别（如RAID 0、RAID 1、RAID 5等）并配置磁盘阵列。

2. **在系统已安装完成后设置RAID级别**：
   - 使用相应的RAID管理工具来配置RAID级别。不同的Linux发行版可能使用不同的RAID管理工具，例如mdadm是一个常用的软件RAID管理工具。
   - 下面是一个使用mdadm设置RAID级别的示例步骤：
     - 安装mdadm工具：`sudo apt-get install mdadm`（Ubuntu）或 `sudo yum install mdadm`（CentOS）。
     - 创建RAID设备：使用`mdadm --create`命令创建RAID设备，并指定RAID级别和要包含的磁盘。
     - 格式化RAID设备：使用`mkfs`命令对RAID设备进行格式化。
     - 挂载RAID设备：将RAID设备挂载到系统中以开始在其上存储数据。

请注意，配置RAID级别涉及数据的备份和迁移，因此在进行此操作之前，请务必备份重要数据以防意外发生。最好在了解清楚RAID级别的特点和适用场景后再进行配置选择。



### 什么是块设备
在操作系统中，硬盘、固态硬盘、光驱等存储设备通常被视为块设备。

块设备是指能够以块（block）为单位进行读写操作的设备，通常用于存储和检索数据。
块设备与字符设备（character device）相对应，字符设备以字符为单位进行读写操作，如键盘、鼠标等。

块设备通过块大小（一般为几 KB 到几 MB）进行数据传输，应用程序可以通过文件系统或直接访问块设备来读取或写入数据。

块设备的特点包括：
1. **随机访问**：块设备支持随机访问，可以直接访问任意位置的数据块，而不需要按顺序逐个访问。
2. **缓存**：块设备通常会在内存中维护一个缓存，用于加速数据的读写操作。
3. **文件系统**：块设备通常用于承载文件系统，文件系统将数据组织成文件并存储在块设备上。

常见的块设备包括硬盘驱动器（HDD）、固态硬盘（SSD）、USB 设备、SD 卡等。块设备的使用范围涵盖了许多领域，包括个人计算机、服务器、嵌入式设备等各种设备和系统。



##### 查询块设备、磁盘容量命令


`lsblk` 命令：
   - `lsblk` 是 "list block devices" 的缩写，用于列出系统中的块设备信息。
   - 输出结果为系统中所有块设备（如硬盘、分区、CD-ROM等）的信息，包括设备名称、大小、挂载点等。
   - 可以用来查看系统中各硬盘的设备信息，包括磁盘大小、挂载情况等。
```shell
[root@VM-4-10-centos bin]# lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sr0     11:0    1 203.7M  0 rom  
vda    253:0    0    50G  0 disk 
└─vda1 253:1    0    50G  0 part /
```

上面能读出什么信息?
>有一个sr0的光盘和一个叫vda的磁盘(主要看TYPE字段),这个磁盘底下有一个vda1分区(part)
>然后这个分区1的挂载点在根目录


### 文件系统

##### 常见文件系统
1. **ext4**：ext4是Linux系统中最常用的文件系统类型之一，是对ext3文件系统的改进版本。它支持更大的文件和分区大小，提供更好的性能和可靠性。ext4是许多Linux发行版的默认文件系统。

2. **XFS**：XFS专注于处理大文件和高性能存储需求，特别适用于大规模数据存储和高性能计算环境。它支持大容量文件系统并具有高效的元数据操作。

3. **FAT32**：FAT32是一种在Windows和Linux系统中都兼容的文件系统类型，适用于移动设备、闪存驱动器等小容量存储设备。然而，FAT32对单个文件大小有限制，不适合用于大容量存储需求。



##### 什么是NFS
>NFS是Network File System 网络文件系统，是一种允许不同计算机之间共享文件和目录的分布式文件系统协议。通过NFS，用户可以在网络上像访问本地文件系统一样访问远程计算机上的文件和目录，实现文件的共享和互操作。

NFS使用RPC（Remote Procedure Call）机制进行通信，基于TCP/IP协议族工作。它提供了对文件和目录的读取、写入、创建、删除等基本操作，使得用户可以方便地在不同计算机之间共享数据。


##### 查询文件系统信息

df 命令：
   - `df` 是 "disk free" 的缩写，用于显示文件系统磁盘空间的使用情况。
   - 输出结果包括：文件系统的挂载点、总容量、已用空间、可用空间、使用率等信息。
   - 适合查看整个系统中各个挂载点（包括分区、硬盘）的空间使用情况。
```shell
[root@VM-4-10-centos bin]# df -h
文件系统        容量      已用  可用 已用% 挂载点
devtmpfs        988M     0  988M    0% /dev
tmpfs          1000M   24K 1000M    1% /dev/shm
tmpfs          1000M  720K  999M    1% /run
tmpfs          1000M     0 1000M    0% /sys/fs/cgroup
/dev/vda1        50G  6.7G   41G   15% /
```
上面能读出什么信息?
>前四个文件系统因为名字是tmpfs,所以存储在内存中, 并不占用磁盘容量
>而/dev/vda1是ext4类型的文件系统, 其本身就是磁盘的一个分区,所以占用磁盘的空间



**查看文件系统类型**
通过使用`df -T`命令可以列出已挂载文件系统的类型。
对于挂载在内存上的文件系统，通常类型为`tmpfs`或`devtmpfs`。
对于挂载在块设备（磁盘）上的文件系统，类型通常为`ext4`、`xfs`、`ntfs`等磁盘文件系统类型。
```shell
[root@VM-4-10-centos bin]# df -T
文件系统       类型        1K-块       已用     可用 已用% 挂载点
devtmpfs       devtmpfs  1011588       0  1011588    0% /dev
tmpfs          tmpfs     1023252      24  1023228    1% /dev/shm
tmpfs          tmpfs     1023252     688  1022564    1% /run
tmpfs          tmpfs     1023252       0  1023252    0% /sys/fs/cgroup
/dev/vda1      ext4     51473868 6931620 42345004   15% /
```

##### 查看目录大小命令
`du` 命令：
`du` 是 "dir usage" 的缩写，用于查看指定目录或文件的磁盘占用大小



#### 文件系统目录树

文件系统目录树是指计算机中文件系统的组织结构，它描述了文件系统中各个目录（或文件夹）之间的层次结构关系。在类Unix系统（如Linux）中，文件系统目录树是一个由根目录（`/`）开始的层级结构，其中包含了所有文件和目录的布局和组织。

文件系统目录树中的每个目录都可以包含文件和其他目录，这样就形成了一个树状结构。用户可以通过遍历不同目录来访问和管理文件系统中的文件和数据。

在典型的Unix文件系统中，常见的一些重要目录包括：

1. `/`（根目录）：整个文件系统的顶级目录，所有其他目录和文件都是从根目录开始的相对路径。

2. `/bin`：存放系统启动和运行时所需的基本命令程序。

3. `/etc`：存放系统的配置文件。

4. `/home`：存放用户的主目录。

5. `/tmp`：存放临时文件。

6. `/var`：存放经常变化的文件，如日志文件等。

7. `/dev`：存放设备文件。


#### 虚拟文件系统

像devtmpfs,tmpfs这些运行在内存中的文件系统,其本身不占用磁盘空间






### 什么是挂载?
>挂载点是指将一个文件系统的根目录连接到另一个文件系统目录树中的特定位置的过程。
通过挂载，可以将一个存储设备或文件系统的内容附加到文件系统目录结构中的某个位置，使得这个存储设备或文件系统的内容能够被访问和管理。

挂载点通常是一个空目录，用于在其下面访问和管理被挂载的文件系统。可以将一个硬盘分区挂载到`/mnt/data`目录下，这样该硬盘分区的内容就会在`/mnt/data`目录下可见。同样，也可以将网络文件系统挂载到`/mnt/nfs`目录下，使得网络存储内容可以在`/mnt/nfs`目录中访问。

##### 挂载的使用场景
获取外部文件系统内的资源, 临时读写
扩充文件系统,扩充存储



##### 挂载方式
1. **手动挂载**：通过命令行手动执行mount命令将磁盘分区挂载到指定的目录。例如，使用`mount /dev/sdXY /mnt/mydrive`将/dev/sdXY分区挂载到/mnt/mydrive目录下。
    
2. **自动挂载**：通过编辑/etc/fstab文件来实现开机自动挂载。在fstab中配置磁盘分区和挂载点的对应关系，系统启动时会自动按照配置信息挂载相应的分区。
    
3. **网络挂载**：挂载远程外部文件系统, 以临时获取资源。这样可以在本地系统上访问远程文件系统中的内容。





##### 挂载usb到文件系统中

将外部硬盘（例如USB硬盘）挂载到系统中的某个目录，以便访问和管理外部硬盘中的数据。

将外部USB硬盘，插入到Linux系统中，并且系统检测到该硬盘为`/dev/sdb1`设备。您希望将这个外部硬盘挂载到`/mnt/usb`目录下，以便访问和管理硬盘中的数据。

1. 首先，创建一个用于挂载的目录`/mnt/usb`：
```bash
sudo mkdir /mnt/usb
```

2. 然后，使用`mount`命令将外部硬盘挂载到`/mnt/usb`目录下：
```bash
sudo mount /dev/sdb1 /mnt/usb
```

现在，外部硬盘的内容就会挂载到`/mnt/usb`目录下，您可以通过`/mnt/usb`目录来访问和管理外部硬盘中的文件和数据。当您不再需要访问外部硬盘时，可以使用`umount`命令将其卸载：
```bash
sudo umount /mnt/usb
```

通过这种方式，可以实现将外部存储设备挂载到系统中的特定目录，从而方便地访问和管理外部设备中的数据。


##### 直接暂时的分区格式化后挂载到文件系统
假设系统中有一块硬盘`/dev/sda`，并且已经用完了该硬盘上的存储空间。想要扩大系统的存储空间，可以通过以下步骤实现磁盘扩容的过程：

1. 添加一块新的硬盘，例如`/dev/sdb`，并对其进行分区（例如将整个硬盘分为一个单独的分区）。

```shell
lsblk -f ##查看本机所有块设备
```

2. 格式化新增硬盘的分区，以便系统能够识别并使用它：
```bash
sudo mkfs.ext4 /dev/sdb1
```

3. 创建一个用于挂载扩展存储空间的目录，例如`/mnt/data`：
```bash
sudo mkdir /mnt/data
```

4. 将新硬盘的分区挂载到创建的目录中：
```bash
sudo mount /dev/sdb1 /mnt/data
```

5. 现在，您可以将`/mnt/data`目录作为额外的存储空间来使用，存储新的文件或者扩展现有的文件系统。

注意:
<font color=#99CCFF style=" font-weight:bold;">对一个块设备挂载的前提是: 这个块设备已经有了文件系统  ,因为挂载是一个文件系统挂载到另一个文件系统的某个目录上</font>


##### 永久的直接挂载
若要实现持久性的磁盘扩容，您可以在系统启动时自动挂载这块新硬盘的分区。可以编辑`/etc/fstab`文件，并在其中添加如下条目：
```plaintext
/dev/sdb1   /mnt/data   ext4   defaults   0   0
```

这样，在每次系统启动时，新硬盘的分区就会被自动挂载到`/mnt/data`目录下，扩展了系统的存储空间。


### 什么是LVM?

>LVM是Linux环境中对磁盘分区进行管理的一种机制，是建立在硬盘和分区之上、文件系统之下的一个逻辑层，可提高磁盘分区管理的灵活性。
利用物理卷来标记硬盘分区, 利用卷组来做抽象层,当加了一个硬盘分区的物理卷, 这个抽象层的总容量就会增加 ,这样就可以用卷组为虚拟卷自由分配扩充的空间


[详细csdn攻略](https://blog.csdn.net/weixin_45960051/article/details/117666736?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171154404516800226551690%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171154404516800226551690&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-117666736-null-null.142^v100^pc_search_result_base3&utm_term=%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9&spm=1018.2226.3001.4187)

[攻略](https://blog.csdn.net/m0_70838473/article/details/124946357?ops_request_misc=&request_id=&biz_id=102&utm_term=%E7%A3%81%E7%9B%98%E6%89%A9%E5%AE%B9&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-124946357.nonecase&spm=1018.2226.3001.4187)


1. 物理卷（Physical Volumes，PV）：<font color=#99CCFF style=" font-weight:bold;">物理卷是实际的磁盘或分区</font>，通过 LVM 标记为物理卷后，可以将其加入到一个卷组中。

2. 卷组（Volume Group，VG）：卷组是由一个或多个物理卷组成的逻辑单元，用于存储逻辑卷。用户可以将多个物理卷组合成一个卷组，以提供更大的存储池。

3. 逻辑卷（Logical Volumes，LV）：<font color=#99CCFF style=" font-weight:bold;">逻辑卷是基于卷组创建的虚拟分区，用户可以在逻辑卷上创建文件系统</font>，就像在物理分区上一样使用。


为啥用lvm?

- 运行时动态调整逻辑卷的大小，无需重新分区或格式化磁盘。
- 将多个物理磁盘整合成一个卷组，方便底层增加,缩减磁盘 不影响到上层逻辑卷。




##### lvm扩充管理磁盘
当你使用LVM（逻辑卷管理器）来扩充磁盘空间时，你需要以下步骤：

1. 确保你有额外的物理存储设备（例如新的硬盘）连接到系统上。
2. 创建一个物理卷（Physical Volume）：使用 `pvcreate` 命令来将新的硬盘分区标记为物理卷。
3. 将物理卷添加到卷组（Volume Group）：使用 `vgextend` 命令将新创建的物理卷添加到现有的卷组中。
4. 扩展逻辑卷（Logical Volume）：使用 `lvextend` 命令来扩展现有的逻辑卷，使其占用新增加的空间。
5. 扩展文件系统：最后，使用适当的文件系统命令（如 `resize2fs`）来扩展文件系统，使其能够利用新增加的空间。

举个具体的例子：

当需要扩展磁盘空间时，可以通过 LVM（Logical Volume Manager）来进行操作。以下是一个具体的例子，假设我们有一块新加入的硬盘 `/dev/sdb`，我们要将其扩展到现有的 LVM 卷组中。

1. 查看当前磁盘和卷组信息
```shell
sudo fdisk -l   # 查看所有磁盘信息，找到要扩展的硬盘
sudo pvs   # 查看物理卷信息
sudo vgs   # 查看卷组信息
sudo lvs   # 查看逻辑卷信息
```
2. 创建新物理卷
```bash
sudo pvcreate /dev/sdb   # 创建新的物理卷
```
3. 将新物理卷添加到现有卷组
```bash
sudo vgextend <卷组名称> /dev/sdb   # 将新物理卷添加到现有卷组
```
4. 扩展逻辑卷
```bash
sudo lvextend -l +100%FREE /dev/<卷组名称>/<逻辑卷名称>   # 将逻辑卷扩展到卷组的所有空闲空间
```
5. 扩展文件系统
```bash
sudo resize2fs /dev/<卷组名称>/<逻辑卷名称>   # 调整文件系统大小以匹配逻辑卷的大小
```

在上面的步骤中，我们首先创建了一个新的物理卷，并将其添加到现有的卷组中。然后，我们通过 `lvextend` 命令来扩展逻辑卷的大小，最后使用 `resize2fs` 命令来调整文件系统的大小以反映逻辑卷的增加。


<font color=#99CCFF style=" font-weight:bold;">总结: 磁盘分区打完一个标记就叫物理卷, 然后将物理卷加到卷组里统一管理,这样就可以给在这个卷组上创建的虚拟卷随意分配空间了,, 然后在这个虚拟卷之上,其实虚拟卷代替了原有的磁盘分区概念,就是动态的虚拟磁盘分区, 所以要在其之上建立文件系统,如果已经建立就可以对这个文件系统进行扩展</font>


练习

```text
a 创建lv1  lv2  分别为100M  200M  实现开机自动挂载  
到/d1  /d2目录下  
 b扩容vg 扩大20G  
 c 重启机器后查看挂载点是否存在？  
 d 取消永久挂载，改为临时挂载。挂载点不变  
 e 将sdb上的逻辑卷迁移到 sdd上  
 f 格式化sdb
```


### 什么是分区
>每个分区可以被看做独立的硬盘, 一个分区损坏,不会影响到其他分区
- **主分区（Primary Partition）**：最多只能有4个主分区，用于安装操作系统或保存用户数据。
    
- **扩展分区（Extended Partition）**：允许创建多个逻辑分区的容器，突破主分区数量限制。
    
- **逻辑分区（Logical Partition）**：位于扩展分区内的分区，数量没有限制，用来存储用户数据


##### 分区查询创建命令
`fdisk` 是一个常用的命令行工具，用于对磁盘进行分区管理。它可以帮助用户创建、编辑、删除磁盘分区，查看分区表信息等。以下是关于 `fdisk` 命令的详细介绍：

 **基本语法：**
   - 命令格式：`fdisk [选项] [设备]`
   - 选项：
     - `-l`：列出所有磁盘的分区信息。
   - 设备：指定要操作的磁盘设备，如 `/dev/sda`。

作用:
   - **启动 `fdisk` 命令**：在终端中输入 `fdisk /dev/sdX`（X代表磁盘字母），进入 `fdisk` 分区管理界面。
   - **查看分区表**：输入 `p` 查看磁盘的分区信息，包括分区号、起始扇区、大小、文件系统类型等。
   - **创建新分区**：输入 `n`，根据提示选择分区类型（主分区、扩展分区、逻辑分区）、起始扇区、大小等参数来创建新分区。
   - **删除分区**：输入 `d`，根据提示选择要删除的分区号，确认后即可删除该分区。
   - **保存修改**：输入 `w`，保存对分区表的修改并退出 `fdisk`。

注意:
   - 在使用 `fdisk` 进行分区操作前，请务必备份重要数据，以免意外导致数据丢失。
   - 谨慎操作，特别是删除分区操作，一旦删除将无法恢复其中的数据。

##### 格式化分区
格式化分区就是给分区建立文件系统
```shell
mkfs.ext4  /dev/adb1     ## 给adb1创建ext4文件系统
```



### 磁盘IO调优总体思路


使用适当的文件系统
Ext4正常稳定、XFS大规模存储和高性能计算

启用磁盘缓存
对于数据安全性不高的情况下,缺点是断电容易丢失数据,所以在处理临时性数据, 对于IO性能要求很强的场景, 顺序读写可以提前缓存大幅提高性能的情况下都能启用  

使用 RAID
通过调节raid级别来提高磁盘的读写,冗余度性能

使用 LVM
适当使用 卷管理器来管理磁盘空间，方便调整和管理逻辑卷。

适当设置磁盘超时时间
对于磁盘读写较慢时,可以延长超时时间, 等待磁盘完成操作
当读写较快时,可缩短超时时间,减少等待时间

调整文件系统写入策略
延迟写入,减少IO



##### 磁盘容量规划
需要考虑磁盘足够的冗余空间和存储数据读写的频率,以及数据的增加速度

### 磁盘性能监控
```
iotop      ##需要下载
iostat
```

##### 监控磁盘IO超标时,写入到日志 举例
当某些磁盘 I/O 性能指标超标时，你可以编写一个脚本或者使用工具来监控这些指标，并将超标情况写入日志文件中。
1. 创建一个名为 monitor_io.sh 的 shell 脚本，内容如下：
```bash
#!/bin/bash

# 监控磁盘IO
threshold=50  # 设置磁盘利用率的阈值，这里设为 50%

while true; do
    io=$(iostat -dx 1 2 | awk 'NR==7{print $14}')  # 使用 iostat 命令获取磁盘利用率
    if (( $(echo "$io > $threshold" | bc -l) )); then  # 比较磁盘利用率是否超过阈值
        echo "磁盘IO性能指标超标，当前利用率为 $io" >> /var/log/io_performance.log  # 将超标信息写入日志文件
    fi
    sleep 5  # 每隔 5 秒监控一次
done
```

2. 授予脚本执行权限：
```bash
chmod +x monitor_io.sh
```

3. 运行脚本来监控磁盘 I/O 性能：
```bash
./monitor_io.sh
```

当磁盘 I/O 性能指标超出设定的阈值时，相关信息将被写入 `/var/log/io_performance.log` 日志文件中。可以根据实际情况调整阈值和监控频率以及日志文件路径等参数。

### 磁盘备份与恢复
备份策略
* 增量备份
* 完整备份

## 待完成栈

**磁盘方面衍生  大体已解决但需要纵向深入的 :**

lvm中的练习还没有练

IO调优需要实际问题进行深入探讨










