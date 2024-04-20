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




### 命令

1. `kubectl get`：用于获取资源的信息，例如获取Pod、Service、Deployment等资源的状态。
2. `kubectl create`：用于创建资源，可以创建Pod、Service、Deployment等资源。
3. `kubectl apply`：用于应用配置更改，能够更新现有资源的配置。
4. `kubectl delete`：用于删除资源，可以删除Pod、Service、Deployment等资源。
5. `kubectl describe`：用于查看资源的详细信息，例如查看Pod的详细配置和状态信息。
6. `kubectl logs`：用于查看Pod的日志信息。
7. `kubectl exec`：用于在容器内部执行命令。

这些是Kubernetes中使用频率较高的几个命令，可以帮助用户进行集群管理和资源操作。


##### 什么是pod
在Kubernetes中，Pod（Pods的复数形式）是最小的部署单元。一个Pod通常包含一个或多个容器，这些容器共享存储、网络等资源，并在同一个节点上运行。Pod可以用来组织、调度和管理容器的生命周期。

每个Pod都有自己的IP地址，可以通过这个IP地址访问Pod内的容器。Pod内的容器共享网络命名空间和存储卷，它们可以通过本地主机上的localhost相互通信。

Pod提供了一种逻辑主机，可以包含一个或多个容器，这些容器共享网络和存储资源，从而支持应用程序的协同工作。Pod的生命周期由Kubernetes的控制器管理，可以根据需要创建、调度和扩展Pod。





[k8s部署](https://blog.csdn.net/hu1010037197/article/details/112416543?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171348912916800225513098%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=171348912916800225513098&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-112416543-null-null.142^v100^pc_search_result_base8&utm_term=k8s%E5%AE%9E%E6%88%98%E6%A1%88%E4%BE%8B&spm=1018.2226.3001.4187)



##### 服务分类
![](img/Pasted%20image%2020240419165834.png)

