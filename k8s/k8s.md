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
用于定义和管理 Pod 的部署。

*  **应用程序部署和更新**：通过定义 Deployment 的模板，用户可以指定要部署的容器镜像、副本数量以及其他配置参数`Deployment` 通过定义 `replicas`、`selector` 和 `template` 来管理 `Pod` 

* . **滚动更新**：Deployment 支持滚动更新策略，允许用户在不中断服务的情况下逐步更新应用程序。用户可以指定要更新的容器镜像，Deployment 会自动创建新的 Pod 实例，并逐步将它们替换为旧的 Pod 实例，直到所有实例都更新完成。

* 用户可以通过修改 Deployment 的副本数量来手动扩展或缩减应用程序的规模。Deployment 可以根据负载情况自动调整 Pod 实例的数量，以满足应用程序的需求。

* **版本控制**：允许用户管理和回滚应用程序的版本。用户可以通过创建新的 Deployment 或回滚到以前的 Deployment 版本来管理应用程序的版本。

**例子**
在Kubernetes中，`Deployment` 资源是用来声明式地管理 `Pod` 和 `ReplicaSet` 的。`Deployment` 通过定义 `spec` 部分来指定如何创建和管理 `Pod`。在你提供的YAML文件中，`Deployment` 的相关部分如下：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: web-app
        image: my-web-app:latest
        ports:
        - containerPort: 80
```

在这段配置中，`Deployment` 管理 `Pod` 生命周期的几个关键点体现在以下几个方面：

1. **replicas**：这个字段指定了 `Pod` 的副本数量，这里设置为3，意味着 `Deployment` 会确保始终有3个 `Pod` 在运行。

2. **selector**：`selector` 字段定义了如何选取 `Pod`。在这里，它使用 `matchLabels` 来选择所有具有 `app: web-app` 标签的 `Pod`。这意味着 `Deployment` 会管理所有带有这个标签的 `Pod`。

3. **template**：`template` 字段定义了 `Pod` 的模板。`Deployment` 会使用这个模板来创建新的 `Pod`。模板中定义了 `Pod` 的 `metadata`（包括标签）和 `spec`（包括容器配置）。

   - **metadata.labels**：这里定义了 `Pod` 的标签，`Deployment` 会根据这些标签来识别和管理 `Pod`。

   - **spec.containers**：这里定义了 `Pod` 中的容器配置，包括容器名称、镜像和端口等。

`Deployment` 通过 `ReplicaSet` 来实现对 `Pod` 的管理。当你创建一个 `Deployment` 时，Kubernetes 会自动创建一个 `ReplicaSet`，然后 `ReplicaSet` 会确保始终有指定数量的 `Pod` 在运行。如果 `Pod` 因为某些原因（如节点故障、镜像更新等）停止运行，`ReplicaSet` 会根据 `Deployment` 的 `template` 创建新的 `Pod` 来替换它们。



##### 有状态和无状态
![](img/Pasted%20image%2020240423093158.png)

有状态服务IP会变 , 但比如主从节点的角色不能变

数据存储不共享

用Headless Service去做DNS服务器, 从而通过服务名发现服务

用volumeClainTemple来创建持久化空间给服务
##### 有状态服务statefulSet

![](img/Pasted%20image%2020240423092633.png)
 StatefulSet 是一种控制器，管理有状态的应用程序的部署。StatefulSet 更适用于需要持久化存储、唯一网络标识符以及有序部署和扩展的应用程序。以下是 StatefulSet 的一些主要用途：

1. **有状态的应用程序管理**：StatefulSet 适用于运行有状态服务的应用程序，如数据库、缓存系统或消息队列。对于这些应用程序，每个实例都有唯一的标识符，并且可能需要访问持久化存储。

2. **唯一的网络标识符**：每个 StatefulSet 实例都会被分配一个稳定的网络标识符（通常是一个 DNS 名称），使得其他应用程序能够通过该标识符轻松地访问到这些实例。这种唯一标识符在故障转移和扩展时保持不变，有助于维护应用程序的稳定性和可用性。

3. **有序的部署和扩展**：StatefulSet 可以确保应用程序的实例按照指定的顺序进行部署和扩展。这对于需要确保每个实例按照特定顺序启动或停止的应用程序非常重要，例如，在主从复制的数据库集群中，可能需要确保首先启动主节点，然后才能启动从节点。

4. **需要存储的应用**：StatefulSet 可以与 PersistentVolumeClaim（持久卷声明）结合使用，以确保每个实例都能够访问持久化存储。例如数据库或文件存储服务。


##### DaemonSet守护进程设置
DaemonSet 保证在每个Node 上都运行一个容器副本，常用来部署—些集群的日志、监控或者其他系统管理应用。典型的应用包括:
* 日志收集，比如fluentd,logstash等
* 系统监控，比如 Prometheus Node Exporter，collectd ,New Relic agent，Ganglia gmond等
* 系统程序，比如kube-proxy, kube-dns, glusterd, ceph 等
### 服务发现

<font color=#99CCFF style=" font-weight:bold;">组件与服务关系:</font>
- `Service` 对象定义了如何访问一组 Pod。
- Kube-DNS 为 `Service` 提供 DNS 解析服务，使得可以通过 DNS 名称访问 `Service`。
- Kube-proxy 实现了 `Service` 的网络转发功能，将请求从 `Service` 转发到后端 Pod。
- 环境变量提供了一种方便的方式，让 Pod 可以直接通过名称访问 `Service`。
- Headless Service 提供了一种机制，允许客户端直接与后端 Pod 通信，而不是通过 `Service` 的单一 IP 地址。
- 负载均衡确保了 `Service` 可以均匀地将流量分配给后端 Pod。
##### Pod通信方式

1. **通过ClusterIP**：
   - `Service`的`type`设置为`ClusterIP`时，这个服务只能在Kubernetes集群内部访问。
   - 集群内部的其他Pod可以通过`Service`的ClusterIP和指定的端口（在这个例子中是80端口）来访问后端服务。

2. **通过NodePort**：
   - 如果你希望从集群外部访问这个服务，可以将`Service`的`type`设置为`NodePort`。
   - Kubernetes会在集群的所有节点上打开一个静态端口（例如30080），并把所有发到这个端口的流量转发到`Service`的端口（80），然后再由`Service`转发到Pod的3000端口。
   - 客户端可以通过`<NodeIP>:<NodePort>`来访问服务，其中`<NodeIP>`是任意一个节点的IP地址，`<NodePort>`是指定的端口。

3. **通过LoadBalancer**（仅限支持负载均衡器的云环境）：
   - 如果你的Kubernetes集群运行在支持外部负载均衡器的云平台上，你可以将`Service`的`type`设置为`LoadBalancer`。
   - 云平台会自动创建一个负载均衡器，并将外部流量转发到`Service`的端口，然后由`Service`转发到Pod。
   - 客户端可以通过负载均衡器提供的公共IP地址和端口来访问服务。

4. **通过Ingress**：
   - 对于更复杂的路由和SSL支持，可以使用`Ingress`资源。
   - `Ingress`可以管理外部访问应用的HTTP和HTTPS路由，支持路径、主机名等级别的路由。

#### Service
<font color=#FFCCCC style=" font-weight:bold;">是什么</font>
Service 通过一个固定的 IP 地址（ClusterIP）和 DNS 名称，<font color=#66CC99 style=" font-weight:bold;">使得 Pod 可以被集群内部或外部的客户端访问</font>，而不需要关心 Pod 的具体位置和数量。Service 还负责在 Pod 之间进行负载均衡。

<font color=#FFCCCC style=" font-weight:bold;">Service 的工作原理</font>

1. **创建 Service**：当创建一个 Service 时，Kubernetes 会自动为该 Service 创建一个虚拟 IP 地址（ClusterIP），这个 IP 地址是固定不变的。
2. **自动更新**：当 Pod 加入或离开 Service 定义的集合时，Service 会自动更新其后端 Pod 的列表。
3. **DNS 记录**：Service 负责在集群内部提供访问时，会为同一个 Service 创建一个 DNS 记录，这个记录将指向该 Service 的虚拟 IP 地址。
4. **请求转发**：当在 Service 的虚拟 IP 地址上收到请求时，请求会被转发给后端 Pod 中的某个实例。

<font color=#99CCFF style=" font-weight:bold;">例子</font>
假设你是一个开发团队的成员，你们团队开发了一个名为“Bookstore”的在线书店应用。这个应用由两个主要部分组成：前端用户界面（UI）和一个后端API服务。现在，你们决定将这个应用部署到Kubernetes集群中。

步骤1：创建Docker镜像

首先，你们团队为前端和后端服务各自创建了Docker镜像，并推送到了Docker Hub或其他容器镜像仓库。假设镜像名称分别为`bookstore/frontend`和`bookstore/backend`。

步骤2：编写Kubernetes YAML配置文件

接下来，你们编写了两个YAML配置文件，一个用于部署前端服务，另一个用于部署后端服务。

**frontend-deployment.yaml**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: bookstore-frontend
  template:
    metadata:
      labels:
        app: bookstore-frontend
    spec:
      containers:
      - name: frontend
        image: bookstore/frontend:latest
        ports:
        - containerPort: 80
```

**backend-deployment.yaml**:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: bookstore-backend
  template:
    metadata:
      labels:
        app: bookstore-backend
    spec:
      containers:
      - name: backend
        image: bookstore/backend:latest
        ports:
        - containerPort: 3000
```

部署应用

现在，你们使用`kubectl`命令行工具将这些YAML配置文件应用到Kubernetes集群中。

在终端或命令行界面，你们执行以下命令：

```shell
kubectl apply -f frontend-deployment.yaml
kubectl apply -f backend-deployment.yaml
```

这些命令告诉Kubernetes：

- 创建一个名为`bookstore-frontend`的Deployment，它会启动2个前端服务的Pod。
- 创建一个名为`bookstore-backend`的Deployment，它会启动3个后端服务的Pod。

步骤4：验证部署

部署完成后，你们可以通过以下命令来检查Pods是否已经成功运行：

```shell
kubectl get pods
```

如果一切正常，你们将看到类似以下的输出：

```
NAME                                READY   STATUS    RESTARTS   AGE
bookstore-frontend-54d67c89d5-xxxxx   1/1     Running   0          2m
bookstore-frontend-54d67c89d5-yyyyy   1/1     Running   0          2m
bookstore-backend-76f49d5c6d5-zzzzz   1/1     Running   0          1m
bookstore-backend-76f49d5c6d5-wwwww   1/1     Running   0          1m
bookstore-backend-76f49d5c6d5-vvvvv   1/1     Running   0          1m
```

这样，你们的“Bookstore”应用就成功部署到了Kubernetes集群中，前端和后端服务都在运行，并且Kubernetes会根据定义的Deployment来管理这些Pod的生命周期。

通过这个案例，你可以看到如何通过`kubectl`工具将YAML配置文件应用到集群中


是的，你的理解是正确的。在Kubernetes中，当你创建了一个`Service`资源并将外部端口（如80或443）映射到Pod的内部端口（如3000）时，你可以通过对`Service`的端口发送请求来间接与Pod通信。

具体来说，当你的`Service`定义如下：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: bookstore-backend-service
spec:
  selector:
    app: bookstore-backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: ClusterIP
```

这里的`Service`会将发送到`Service`的80端口的流量转发到后端Pod的3000端口。这个过程对客户端是透明的，客户端不需要知道后端Pod的具体IP地址或它们内部使用的端口号。

当你创建了一个`Service`资源并将外部端口（如80或443）映射到Pod的内部端口（如3000）时，你可以通过对`Service`的端口发送请求来间接与Pod通信。

具体来说，当你的`Service`定义如下：

yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: bookstore-backend-service
spec:
  selector:
    app: bookstore-backend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: ClusterIP
```

这里的`Service`会将发送到`Service`的80端口的流量转发到后端Pod的3000端口。这个过程对客户端是透明的，客户端不需要知道后端Pod的具体IP地址或它们内部使用的端口号。

- **集群内部**：可以直接使用 Service 的 ClusterIP 访问 Nginx 服务。
- **集群外部**：如果需要从集群外部访问，可以创建一个 NodePort 类型的 Service，如下所示（service-nodeport.yaml）：



```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      nodePort: 30002
      targetPort: 80
  type: NodePort
```

应用这个配置文件，创建 NodePort Service：

```text
kubectl apply -f service-nodeport.yaml
```

然后，可以通过 `<任意节点IP>:30002` 访问 Nginx 服务。

#### Ingress
<font color=#FFCCCC style=" font-weight:bold;">是什么</font>
用于管理外部访问应用的规则集
用来处理集群外部进入集群的HTTP和HTTPS流量
业务需要复杂的HTTP路由、基于路径或主机名的路由、SSL终止等高级功能就用它


<font color=#FFCCCC style=" font-weight:bold;">Ingress 的工作流程</font>

1. **定义 Ingress 资源**：用户创建 Ingress 资源，定义主机名、路径和后端服务的映射。
3. **配置代理服务器**：根据 Ingress 资源的定义，Ingress 控制器配置其代理服务器（如 NGINX）以匹配请求。
3. **处理请求**：当外部请求到达集群时，Ingress 控制器的代理服务器根据 Ingress 规则进行路由，将请求转发到相应的服务。

<font color=#99CCFF style=" font-weight:bold;">例子</font>
多服务管理
**场景**：在一个集群中运行多个应用，需要通过不同的域名或子域名进行访问。

**例子**：假设有两个应用，一个电商网站和一个API服务，分别部署在 Kubernetes 集群中。我们希望用户通过 `www.example.com` 访问电商网站，通过 `api.example.com` 访问API服务。

**Ingress 配置**：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: www.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

在这个例子中，我们定义了一个名为 `example-ingress` 的 Ingress 资源，它有两个规则：一个用于将 `www.example.com` 的流量转发到名为 `web-service` 的服务，另一个用于将 `api.example.com` 的流量转发到名为 `api-service` 的服务。


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



1. **安装和配置Fiddler** - 首先，从官方网站（https://www.telerik.com/download/fiddler）下载并安装Fiddler。 - 安装完成后，打开Fiddler。在菜单栏中选择“Tools” -> “Options”。 - 在“HTTPS”选项卡中，确保“Capture HTTPS CONNECTs”和“Decrypt HTTPS traffic”选项被勾选，这样才能捕获和解析HTTPS请求。Fiddler会提示安装证书，按照提示安装即可，这是为了能够正确地捕获加密的https请求。 2. **设置手机代理（针对微信小程序在手机端运行的情况）** - 确定你的手机和电脑处于同一局域网内。 - 查看电脑的IP地址，可以通过在命令提示符（Windows）或终端（Mac）中输入`ipconfig`（Windows）或`ifconfig`（Mac）来查看。 - 在手机的Wi - Fi设置中，找到当前连接的Wi - Fi网络，长按或点击其详细信息，找到“代理”选项，将其设置为“手动”，并在“服务器”字段中输入电脑的IP地址，在“端口”字段中输入Fiddler的默认监听端口8888。 3. **捕获微信小程序请求** - 打开微信小程序图书馆签到系统，在Fiddler中就可以看到小程序发送的各种请求。 - 仔细观察请求列表，找到签到相关的请求。可以根据请求的URL路径（例如包含“sign - in”“check - in”等关键词的路径）、请求方法（通常是POST用于提交签到信息）和请求头中的信息来判断。 4. **分析签到请求细节** - 点击签到相关的请求，在右侧的“Inspectors”选项卡中查看请求的详细信息。 - 在“Headers”部分，可以看到请求头，包括用户代理（User - Agent）、内容类型（Content - Type）等重要信息。 - 在“WebForms”或“TextView”部分（取决于请求的格式），可以看到请求体中的参数，如用户ID、时间戳、签名等可能用于签到验证的信息。 5. **模拟签到请求（使用其他工具）** - 根据Fiddler捕获和分析的信息，使用编程语言和相关的HTTP请求库（如Python的requests库）来模拟签到请求。 - 例如，假设签到请求是POST方法，URL是`https://library - app.com/sign - in`，请求体中有`user_id`和`timestamp`两个参数，使用Python的requests库可以这样模拟请求： ```python import requests url = "https://library - app.com/sign - in" data = { "user_id": "your_user_id", "timestamp": "current_timestamp" } headers = { "User - Agent": "WeChat MiniProgram/7.0.0 (your_user_agent_details)" } response = requests.post(url, data=data, headers=headers) print(response.text) ``` - 注意，上面的代码中`your_user_id`、`current_timestamp`和`your_user_agent_details`需要根据Fiddler捕获的实际信息进行替换。同时，可能还需要处理其他的验证信息，如签名等，这可能需要进一步分析小程序的加密和验证机制。