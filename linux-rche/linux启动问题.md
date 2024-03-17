
## linux启动流程
在 Linux 启动时，会执行一系列的文件和脚本，这些文件的执行顺序可能有所不同，具体取决于使用的发行版和初始化系统。以下是一般情况下 Linux 启动时可能执行的一些关键文件和目录：

1. **BIOS/UEFI**: 在启动过程中，首先由 BIOS 或 UEFI 进行硬件自检，并加载引导程序（Boot Loader）。
    
2. **Boot Loader 配置文件**：比如 GRUB 的配置文件 `/boot/grub/grub.cfg`，用于指定内核和 initramfs 的位置等信息。
    
3. **内核（Kernel）**：启动后，加载并执行 Linux 内核。
    
4. **Systemd**：在现代 Linux 发行版中，Systemd 会接管启动过程，根据 Unit 文件定义的内容来管理系统服务。


### bootloader的配置文件grub
在以下情况下，你可能需要更改 GRUB 的默认启动选项：

1. **测试新的内核版本**: 当你安装了一个新的内核版本，并希望在系统启动时默认选择该新内核以进行测试和验证时，可以将该内核设置为默认启动选项。
    
2. **回退到稳定的内核**: 如果系统在更新后出现问题，你可能需要回退到之前使用的稳定内核版本。通过将稳定内核版本设为默认启动选项，可以方便地回到之前的系统状态。
    
3. **多重操作系统选择**: 如果你的系统上安装了多个操作系统（如双系统或多重引导），你可能会希望将特定操作系统设置为默认启动选项以简化引导过程。
    
4. **优化系统性能**: 有些内核参数可以用来优化系统性能或解决特定问题。通过修改默认启动选项中的内核参数，你可以调整系统的行为以达到更好的性能或稳定性。
    
5. **自定义引导选项**: 在某些情况下，你可能需要添加自定义的引导选项，如单用户模式、恢复模式等。将这些选项设置为默认启动可以简化系统维护和故障排除过程。

### Systemd Unit 文件配置
以一个常见的服务“nginx”为例来深入讨论如何修改 Systemd Unit 文件。

**1. 查看 nginx 的 Unit 文件**

通常 nginx 的 Unit 文件位于 `/lib/systemd/system/nginx.service` 或 `/etc/systemd/system/nginx.service` 中，我们可以使用以下命令查看该文件：

```bash
cat /lib/systemd/system/nginx.service
```

**2. 修改 Systemd Unit 文件**

假设我们要修改 nginx 服务的一些配置，比如修改启动参数或其他设置。我们可以编辑 Unit 文件，比如使用 `sudo nano /lib/systemd/system/nginx.service` 命令来打开该文件进行修改。

**3. 示例：禁止自动重启**

如果我们希望禁止 nginx 服务在意外退出时自动重启，可以在 Unit 文件的 `[Service]` 部分添加如下参数：

```plaintext
Restart=on-failure
RestartSec=5s
StartLimitBurst=10
StartLimitInterval=60s
```

这些参数将告诉 Systemd 在服务失败时不自动重启，等待 5 秒后再尝试重启，最多重启 10 次，每次之间间隔 60 秒。

**4. 保存修改并重新加载 Systemd**

完成修改后，保存文件并通过以下命令告诉 Systemd 重新加载配置：

```bash
sudo systemctl daemon-reload
```

**5. 重新启动 nginx 服务**

最后，使用以下命令重启 nginx 服务以使修改生效：

```bash
sudo systemctl restart nginx
```

这样，我们通过修改 Systemd Unit 文件成功地定制了 nginx 服务的行为。你可以根据需要进一步探索 Systemd Unit 文件中的其他配置选项来满足不同的需求


#### Systemd中有哪些单元
在 systemd 中，有几种常用的单元类型，以下是其中一些常用的单元类型：

1. **.service 单元**：
    
    - .service 单元用于描述和管理系统中的服务，是最常见和最常用的单元类型。通过.service 单元，可以定义启动、停止、重启服务的命令，设置服务的相关配置等。
2. **.target 单元**：
    - .target 单元用于描述系统中的目标状态，代表系统处于特定状态或运行级别。比如 multi-user.target 表示多用户模式下的系统状态。.target 单元在控制系统启动和运行级别时非常有用。
3. **.mount 单元**：
    
    - .mount 单元用于描述和管理文件系统挂载点，可以指定文件系统的挂载点、类型、选项等信息。这在管理挂载点时非常实用。
4. **.timer 单元**：
    
    - .timer 单元用于管理定时器，可以定期触发特定操作或单元的执行。例如，可以使用.timer 单元来定期执行备份任务或其他定时任务。
#### Systemd中的service单元是什么

**什么是service单元?**
在 systemd 中，.service 单元是一种配置文件，用于描述和管理系统中的一个服务。这个服务可以是系统自带的服务，也可以是用户自定义的服务。.service 单元文件包含了服务的各种配置选项，如启动命令、停止命令、环境变量等，以及服务的依赖关系和其他相关信息。

通过.service 单元文件，systemd 可以管理和控制系统中的各种服务，如网络服务、数据库服务、Web 服务器等。管理员可以使用 systemctl 命令来启动、停止、重启、重新加载这些服务，也可以查看服务的状态、日志等信息。

#### 怎么查看对应服务的service单元的配置文件
在 systemd 中，服务的 .service 单元配置文件通常存储在以下位置：

1. **系统服务**：系统自带的服务（如网络服务、日志服务等）的 .service 单元配置文件通常存储在 `/lib/systemd/system/` 目录下。一般情况下，这些配置文件是只读的，不建议直接修改。
    
2. **本地服务**：用户自定义的服务的 .service 单元配置文件通常存储在 `/etc/systemd/system/` 目录下。在这个目录中，你可以创建和编辑自己的服务配置文件，以定义和管理自己的服务。
    

一般来说，如果你想新增、编辑或删除一个服务的配置，可以通过在以上路径中创建、修改或删除相应的 .service 单元文件来实现。修改完之后，记得使用 `systemctl daemon-reload` 命令重新加载 systemd 的配置，使更改生效。

另外，可以使用 `systemctl list-unit-files` 命令来查看系统中所有已安装的单元文件，包括服务单元文件。通过这个命令可以查看系统中存在哪些服务，并了解它们的状态和启用情况。

#### .service配置文件中需要改啥?

在运维工作中，有一些.service单元文件中的配置项可能需要经常调整或关注，具体取决于不同的服务和环境。以下是一些经常需要运维人员关注和调整的配置选项：

1. **ExecStart 和 ExecStop**：
    
    - 运维人员可能需要调整服务启动时执行的命令（ExecStart）或停止时执行的命令（ExecStop），以确保服务能够按照需求正确启动和停止。
2. **Restart 相关配置**：
    
    - 根据具体需求和情况，可能需要配置服务在发生故障或异常时的重启策略，比如设置重启延迟时间（RestartSec）、最大重启次数等。
3. **User 和 Group**：
    
    - 指定服务运行的用户和用户组。有时候会根据安全性、权限等考虑需要调整服务运行的用户身份。
4. **Environment**：
    
    - 配置服务所需的环境变量，可能会根据需要添加、修改环境变量，以确保服务正常运行。
5. **WantedBy**：
    
    - 确保服务正确安装到指定的 target 中，可能需要根据实际情况进行调整。
 6. after:
	 * 确保在对应程序启动后才执行该程序

这些是在运维工作中可能需要经常调整的一些.service单元文件中的配置选项，确保这些配置项与服务的实际需求和运行环境保持一致，有助于确保服务的正常运行。

#### 为mysql编写service unit配置
1. 首先，创建一个名为 `mysql.service` 的服务单元文件，通常存储在 `/etc/systemd/system/` 目录下（如果是系统自带的 MySQL 服务，可能会在 `/lib/systemd/system/` 目录下）。
    
2. 编辑 `mysql.service` 文件并添加以下内容：
    

```bash
[Unit]
Description=MySQL Database Server
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/mysqld --daemonize
ExecReload=/bin/kill -HUP $MAINPID
ExecStop=/usr/bin/mysqladmin shutdown
User=mysql
Group=mysql
Restart=always

[Install]
WantedBy=multi-user.target
```

一般来说，如果你想新增、编辑或删除一个服务的配置，可以通过在以上路径中创建、修改或删除相应的 .service 单元文件来实现。修改完之后，记得使用 `systemctl daemon-reload` 命令重新加载 systemd 的配置，使更改生效。

3. 保存文件后，可以使用以下命令控制 MySQL 服务：

- `sudo systemctl start mysql`：启动 MySQL 服务
- `sudo systemctl stop mysql`：停止 MySQL 服务
- `sudo systemctl restart mysql`：重启 MySQL 服务
- `sudo systemctl status mysql`：查看 MySQL 服务状态
- `sudo systemctl enable mysql`：设置 MySQL 服务开机自启动


##### 为nginx编写service单元配置

1. 创建一个新的 systemd 服务单元文件，比如 `nginx.service`，一般存放在 `/etc/systemd/system/` 目录下。
    
2. 使用编辑器打开该文件并添加以下内容：
    

```shell
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

3. 保存并关闭文件。然后重新加载 systemd，使其检测到新的服务单元文件：

```bash
sudo systemctl daemon-reload
```

4. 启用 nginx 服务，并启动它：

```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

## 创建一个系统刚启动时自动执行的脚本
要定义一个系统启动后自动执行的脚本，你可以使用 systemd 来创建一个服务单元文件。下面是一个简单的步骤指南：

1. 创建一个新的服务脚本，比如 `myscript.sh`，并编写你想要在系统启动后执行的脚本内容。
    
2. 将这个脚本文件放在一个合适的位置，比如 `/usr/local/bin/myscript.sh`。
    
3. 创建一个 systemd 服务单元文件，比如 `myscript.service`，用来描述你的脚本服务。在终端执行以下命令创建并编辑该文件：
    

```shell
sudo vi /etc/systemd/system/myscript.service
```

4. 在 `myscript.service` 文件中输入以下内容：

```shell
[Unit]
Description=My Script
After=network.target

[Service]
ExecStart=/usr/local/bin/myscript.sh

[Install]
WantedBy=multi-user.target
```

在上面的内容中，你需要将 `ExecStart` 指定为你的脚本文件的路径，`Description` 是对该服务的描述，`After` 表示该服务应该在哪些服务之后启动，`WantedBy` 表示该服务应该在哪个 target 下启动。

5. 保存文件并退出编辑器。
    
6. 使用以下命令启用该服务并立即启动：
    

```
sudo systemctl enable myscript.service
sudo systemctl start myscript.service
```