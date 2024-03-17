

#### 进程监控

```shell
ps -f ##查看主要运行的进程
ps -ef  ##查看所有进程,一般用grep过滤
```

ps代表"process status"，通过该命令可以列出系统中当前运行的进程信息。

以下是ps命令的常见选项和功能：

1. **显示当前用户的进程**：默认情况下，ps命令会显示当前用户的所有进程。
    
2. **显示所有用户的进程**：使用`ps -e`或`ps aux`可以显示所有用户的进程，而非仅限于当前用户。
    
3. **显示完整格式**：使用`ps -f`可以显示更加详细的进程信息，包括PID（进程ID）、PPID（父进程ID）、UID（用户ID）、CPU利用率、内存占用等。
    
4. **显示所有进程及线程**：通过`ps -eLf`可以显示系统中所有进程及其线程的信息。
    
5. **显示进程树状结构**：使用`ps -ejH`或`ps axjf`可以显示进程之间的父子关系，以树状结构展示进程间的关系。
    
6. **显示进程树和进程组ID**：通过`ps -ejH`可以显示进程树、PID以及PGID（进程组ID）的信息。
    
7. **按内存占用排序**：使用`ps aux --sort -rss`可以按照内存占用大小对进程进行排序。
    
8. **实时刷新进程信息**：使用`ps -aux`结合管道和`watch`命令可以实现实时刷新进程信息的效果。
    
9. **定时显示进程信息**：结合crontab和ps命令，可以定时监控并记录系统中的进程信息。


>正在执行的命令本身就是进程
```shell
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

#### cpu内存监控top命令

查看当前所有进程的cpu,内存占用率
根据负载可以判断出cpu的使用率高,io出问题了
```shell
top
```

```shell
PID USER      PR  NI    VIRT    RES    SHR S  %CPU   %MEM     TIME+ COMMAND                                                                                                                                   
 9033 root      20   0 1022676  83724  13616 S   2.3  4.1 447:45.41 YDService                                                                                                                                 
23666 root      20   0  757320  17464   2624 S   1.0  0.9 453:12.50 barad_agent                                                                                                                               
 9    root      20   0       0      0      0 S   0.3  0.0 138:49.19 rcu_sched  
```

1. **PID（Process ID）**：进程ID，标识每个进程的唯一数字。
   
3. **PR（Priority）**：进程优先级。
   
4. **NI（Nice value）**：进程的优先级值，负值表示高优先级，正值表示低优先级。
   
5. **VIRT（Virtual Memory）**：进程使用的虚拟内存量。
   
6. **RES（Resident Memory）**：进程实际使用的物理内存量。
   
7. **SHR（Shared Memory）**：进程使用的共享内存量。
   
8. **%CPU**：进程占用CPU的百分比。
   
9. **%MEM**：进程占用内存的百分比。
   
10. **TIME+**：进程已经执行的CPU时间。
    
11. **COMMAND**：启动进程的命令名称。

## 查看网络相关信息
```shell
netstat -tulpn
```

1. **netstat -a**：显示所有活动的网络连接和监听端口。
2. **netstat -t**：显示所有TCP协议相关的网络连接。
3. **netstat -u**：显示所有UDP协议相关的网络连接。
4. **netstat -n**：以数字形式显示地址和端口号，而不解析为域名或服务名。
5. **netstat -p**：显示与连接相关的进程ID（PID）和进程名称。
6. **netstat -r**：显示系统的路由表。
7. **netstat -s**：显示网络统计信息，如传输数据包的数量、错误等。
8. **netstat -i**：显示网络接口的信息，如收发数据包的统计信息和错误信息

**netstat -tulpn**：这个命令选项会列出所有TCP和UDP协议的网络连接信息，包括IP地址、端口号以及对应的进程ID（PID）。其中，"-t"表示只显示TCP协议相关信息，"-u"表示只显示UDP协议相关信息，"-p"表示显示进程名或PID，"-n"表示以数字形式显示地址和端口号而不解析为域名或服务名。
"l"选项，表示只显示监听中的连接。

```shell
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:6379            0.0.0.0:*               LISTEN      8932/docker-proxy   
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      24041/docker-proxy  
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1374/sshd           
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      1307/master         
tcp6       0      0 :::6379                 :::*                    LISTEN      8938/docker-proxy   
tcp6       0      0 :::8080                 :::*                    LISTEN      24047/docker-proxy  
```