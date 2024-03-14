
##### linux启动过程
硬件层面就是启动bios然后是bootloader
然后加载内核空间,启动的一个用户进程init,init会启动系统服务和进程
然后加载用户空间,进入多用户模式

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

