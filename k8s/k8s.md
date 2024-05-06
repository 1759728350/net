**不需要会部署,主要会用**
![](img/Pasted%20image%2020240419200807.png)


##### 组件
![](img/Pasted%20image%2020240419170054.png)

1. **kube-apiserver**：作为 Kubernetes 控制面的入口，负责提供 API 服务，并处理集群状态的更改请求。

2. **etcd**：一个分布式键值存储，用于保存 Kubernetes 集群的状态数据。

3. **kube-scheduler**：负责将新创建的 Pod 调度到集群中的节点上。

4. **kube-controller-manager**：包含多个控制器，负责处理集群中的各种控制循环，比如节点控制器、副本控制器等。

5. **kubelet**：运行在每个节点上，负责管理 Pod 和容器，以及与容器运行时（比如 Docker）交互。

6. **kube-proxy**：负责为 Pod 提供网络代理和负载均衡服务。

7. **Container Runtime**：比如 Docker、containerd 等，用于运行容器。

8. **CoreDNS** 或 **kube-dns**：负责集群内部的 DNS 服务，提供服务发现和解析。

9. **Ingress Controller**：负责暴露集群内服务给集群外部，实现负载均衡和路由。

10. **Dashboard**：提供集群的 Web 界面管理工具。

这些组件共同协作，构成了 Kubernetes 集群的核心架构，使得 Kubernetes 能够实现自动化部署、扩展和管理容器化应用程序。每个组件都有特定的功能和角色，相互配合工作以确保整个集群的正常运行。如果你对其中任何一个组件或其之间的关系有具体的疑问，欢迎提出，我会尽力解答。

### 主节点

在Kubernetes集群的Master节点上通常包含以下主要组件：

1. **Kubernetes API Server**：kube-apiserver是Kubernetes集群的API服务端，负责提供对外的RESTful API接口，管理集群状态、接收和处理用户请求，并将请求转发给其他组件处理。

2. **Kubernetes Controller Manage**：kube-controller-manager是一组控制器的集合，负责监控集群中的各种资源对象（如Pod、ReplicationController、Namespace等），并确保集群状态与期望状态保持一致。

3. **Kubernetes Scheduler**：kube-scheduler负责根据预定义的调度策略，将新创建的Pod调度到集群中的合适节点上运行。它考虑了节点资源的可用性、Pod的资源需求、调度约束等因素。

4. **etcd**：etcd是Kubernetes集群的分布式键值存储数据库，用于存储集群的配置信息、状态信息、元数据等。etcd提供了高可用性、一致性和持久性的存储服务，是Kubernetes的重要组件之一。

##### controller manager
Kubernetes Controller Manager是Kubernetes集群中的一个核心组件，它是一组控制器（Controllers）的集合，负责监控集群中的各种资源对象，并确保集群状态与期望状态保持一致。简单来说，Controller Manager负责管理和控制Kubernetes中的各种资源对象，确保它们按照用户定义的期望状态运行。

Controller Manager包含了多个控制器，每个控制器负责监控和管理一个或多个特定类型的资源对象，常见的控制器包括：

1. Replication Controller：负责监控Pod副本数量，确保在集群中按照用户定义的副本数量运行Pod。

2. Replica Set Controller：与Replication Controller类似，负责管理Pod的副本数量，但具有更强大的选择器匹配功能。

3. Deployment Controller：负责管理应用程序的部署和更新，通过控制Replica Set来实现滚动更新和回滚操作。

4. Stateful Set Controller：用于管理有状态的应用程序，确保这些应用程序的状态和标识持久化，并按照定义的顺序进行部署和扩展。

5. Daemon Set Controller：负责在集群中的每个节点上运行一个副本的Pod，常用于部署守护进程和后台任务。

6. Job Controller：用于管理一次性任务的执行，确保任务成功完成或失败后能够重新执行。

Controller Manager通过不断地对资源对象的状态进行监控和调节，实现了集群的自动化管理和自愈能力，确保集群的高可用性和稳定性。



### 从节点

1. **kubelet**：kubelet是运行在每个节点上的Kubernetes代理，负责管理节点上的Pod和容器生命周期。它与Master节点通信，执行Master节点下发的指令，监控Pod的运行状态，并定期报告节点状态。

2. **kube-proxy**：kube-proxy是负责为Pod和Service提供网络代理和负载均衡功能的组件。它在每个节点上运行，维护了集群内部的网络规则、转发规则和连接表，实现了集群内服务之间的通信。

3. **Container Runtime**：Container Runtime是负责运行容器的软件，常见的包括Docker、containerd、cri-o等。它负责管理和执行Pod中的容器，提供了容器的生命周期管理、资源隔离、网络配置等功能。

4. **Pod**：Pod是Kubernetes中最小的调度单元，它可以包含一个或多个紧密相关的容器。Pod在Node节点上运行，由kubelet负责管理和监控其生命周期。


### 资源和对象
#### 资源分类
![](img/Pasted%20image%2020240420161132.png)

#### 元空间
![](img/Pasted%20image%2020240420162245.png)
#### 集群级
![](img/Pasted%20image%2020240420162225.png)
## 命名空间级
![](img/Pasted%20image%2020240420162049.png)

强耦合放一个pod中,一般一个服务容器放一个pod里,
无状态选择上面适用无状态的控制器,
### 什么是pod
在Kubernetes中，Pod（Pods的复数形式）是最小的部署单元。一个Pod通常包含一个或多个容器，这些容器共享存储、网络等资源，并在同一个节点上运行。Pod可以用来组织、调度和管理容器的生命周期。

每个Pod都有自己的IP地址，可以通过这个IP地址访问Pod内的容器。Pod内的容器共享网络命名空间和存储卷，它们可以通过本地主机上的localhost相互通信。

Pod提供了一种逻辑主机，可以包含一个或多个容器，这些容器共享网络和存储资源，从而支持应用程序的协同工作。Pod的生命周期由Kubernetes的控制器管理，可以根据需要创建、调度和扩展Pod。
### 副本replicas

在 Kubernetes 中，replicas 指的是 Pod 的副本数量。Pod 是 Kubernetes 中最小的部署单元，它可以包含一个或多个容器，并且通常用于运行应用程序或服务。

当你创建一个 Deployment、ReplicaSet 或 StatefulSet 时，你通常会指定要创建的 Pod 的副本数量。这个副本数量就是 replicas。例如，在一个 Deployment 中，你可以定义希望运行的 Pod 实例数量，Kubernetes 将会负责创建并管理这些实例，确保它们的数量与你指定的副本数量保持一致。

通过控制 Pod 的副本数量，你可以实现以下目标：

1. **高可用性**：通过创建多个副本，即使某些 Pod 发生故障，也可以确保应用程序的其他副本继续提供服务，从而提高了应用程序的可用性。

2. **负载均衡**：当你有多个副本时，Kubernetes 可以自动将请求分配到这些副本之间，从而实现负载均衡。这有助于确保应用程序的吞吐量和性能。

3. **水平扩展**：通过增加或减少副本数量，你可以实现应用程序的水平扩展或缩减。当负载增加时，可以增加副本数量以应对高流量，当负载减少时，可以减少副本数量以节省资源。

总的来说，replicas 在 Kubernetes 中用于控制 Pod 的数量，通过调整副本数量，你可以实现高可用性、负载均衡和水平扩展等目标，从而更有效地管理和运行容器化的应用程序。
### 控制器
##### 无状态服务Deployment
Kubernetes 中的 Deployment 是一种控制器，用于定义和管理 Pod 的部署。Deployment 提供了一种声明式的方法来定义 Pod 的期望状态，并确保集群中的实际状态与期望状态保持一致。以下是 Deployment 的一些主要用途：

1. **应用程序部署和更新**：Deployment 允许用户轻松地部署新的应用程序或更新现有的应用程序。通过定义 Deployment 的模板，用户可以指定要部署的容器镜像、副本数量以及其他配置参数，Kubernetes 会负责创建和管理相应数量的 Pod 实例。

2. **滚动更新**：Deployment 支持滚动更新策略，允许用户在不中断服务的情况下逐步更新应用程序。用户可以指定要更新的容器镜像，Deployment 会自动创建新的 Pod 实例，并逐步将它们替换为旧的 Pod 实例，直到所有实例都更新完成。

3. **故障恢复**：Deployment 具有自我修复的能力，可以监控 Pod 实例的健康状态，并在发生故障时自动重启或替换受影响的实例。这有助于提高应用程序的可靠性和可用性。

4. **扩展性**：Deployment 可以根据负载情况自动调整 Pod 实例的数量，以满足应用程序的需求。用户可以通过修改 Deployment 的副本数量来手动扩展或缩减应用程序的规模。

5. **版本控制**：Deployment 具有版本控制的功能，允许用户轻松地管理和回滚应用程序的版本。用户可以通过创建新的 Deployment 或回滚到以前的 Deployment 版本来管理应用程序的版本。

总的来说，Deployment 是 Kubernetes 中用于管理应用程序部署和更新的核心组件，提供了灵活的部署策略、自动故障恢复和扩展性能力，帮助用户更轻松地管理容器化的应用程序。
##### 有状态和无状态
![](img/Pasted%20image%2020240423093158.png)

有状态服务IP会变 , 但比如主从节点的角色不能变

数据存储不共享

用Headless Service去做DNS服务器, 从而通过服务名发现服务

用volumeClainTemple来创建持久化空间给服务
##### 有状态服务statefulSet

![](img/Pasted%20image%2020240423092633.png)
Kubernetes 中的 StatefulSet 是一种控制器，用于管理具有状态的应用程序的部署。与 Deployment 不同，StatefulSet 更适用于需要持久化存储、唯一网络标识符以及有序部署和扩展的应用程序。以下是 StatefulSet 的一些主要用途：

1. **有状态的应用程序管理**：StatefulSet 适用于运行有状态服务的应用程序，如数据库、缓存系统或消息队列。对于这些应用程序，每个实例都有唯一的标识符，并且可能需要访问持久化存储。

2. **唯一的网络标识符**：每个 StatefulSet 实例都会被分配一个稳定的网络标识符（通常是一个 DNS 名称），使得其他应用程序能够通过该标识符轻松地访问到这些实例。这种唯一标识符在故障转移和扩展时保持不变，有助于维护应用程序的稳定性和可用性。

3. **有序的部署和扩展**：StatefulSet 可以确保应用程序的实例按照指定的顺序进行部署和扩展。这对于需要确保每个实例按照特定顺序启动或停止的应用程序非常重要，例如，在主从复制的数据库集群中，可能需要确保首先启动主节点，然后才能启动从节点。

4. **持久化存储**：StatefulSet 可以与 PersistentVolumeClaim（持久卷声明）结合使用，以确保每个实例都能够访问持久化存储。这对于需要数据持久性的应用程序非常重要，例如数据库或文件存储服务。

总的来说，StatefulSet 在 Kubernetes 中扮演着管理有状态应用程序的重要角色，提供了稳定的网络标识符、有序的部署和扩展以及持久化存储的能力，从而帮助用户更轻松地部署和管理复杂的有状态应用程序。
##### DaemonSet守护进程设置
DaemonSet 保证在每个Node 上都运行一个容器副本，常用来部署—些集群的日志、监控或者其他系统管理应用。典型的应用包括:
* 日志收集，比如fluentd,logstash等
* 系统监控，比如 Prometheus Node Exporter，collectd ,New Relic agent，Ganglia gmond等
* 系统程序，比如kube-proxy, kube-dns, glusterd, ceph 等

## 命令

1. `kubectl get`：用于获取资源的信息，例如获取Pod、Service、Deployment等资源的状态。
2. `kubectl create`：用于创建资源，可以创建Pod、Service、Deployment等资源。
3. `kubectl apply`：用于应用配置更改，能够更新现有资源的配置。
4. `kubectl delete`：用于删除资源，可以删除Pod、Service、Deployment等资源。
5. `kubectl describe`：用于查看资源的详细信息，例如查看Pod的详细配置和状态信息。
6. `kubectl logs`：用于查看Pod的日志信息。
7. `kubectl exec`：用于在容器内部执行命令。

这些是Kubernetes中使用频率较高的几个命令，可以帮助用户进行集群管理和资源操作。







[k8s部署](https://blog.csdn.net/hu1010037197/article/details/112416543?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171348912916800225513098%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171348912916800225513098&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-112416543-null-null.142^v100^pc_search_result_base8&utm_term=k8s%E5%AE%9E%E6%88%98%E6%A1%88%E4%BE%8B&spm=1018.2226.3001.4187)



##### 服务分类
![](img/Pasted%20image%2020240419165834.png)

