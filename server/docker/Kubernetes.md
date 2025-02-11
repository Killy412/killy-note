# K8s

### 基本概念
k8s绝大部分概念都抽象成k8s管理的一种资源对象

- Master: k8s集群的控制节点,负责整个集群的管理和控制,包含以下组件
  - kube-apiserver: 集群控制的入口,提供http rest服务
  - kube-controller-manager: k8s集群中所有资源对象的自动化控制中心
  - kube-scheduler: 负责pod的调度
- Node: k8s集群中的工作节点,node上工作负载由master分配,工作负载主要是运行容器应用
  - kubelet: 负责pod的创建/启动/监控/销毁/重启等工作,同时与master节点协作,实现集群管理的基本功能
  - kube-service: 实现k8s service的通信和负载均衡
  - 运行容器化(Pod)应用
-  Pod: 是k8s最基本的部署调度单元.一个pod表示某个应用的一个实例
-  ReplicaSet: pod副本的抽象,解决pod的扩容和伸缩
-  Deployment: 表示部署,内部使用ReplicaSet创建.通过Deployment生成相应的ReplicaSet完成pod副本的创建
-  Service: 是k8s最重要的资源对象.k8s中的service对应微服务架构中的微服务.service定义服务的访问入口,服务的调用者通过这个地址访问 Service 后端的 Pod 副本实例。Service 通过 Label Selector 同后端的 Pod 副本建立关系，Deployment 保证后端Pod 副本的数量，也就是保证服务的伸缩性。

k8s有以下几个核心组件

- etcd保存整个集群的状态,就是一个数据库
- apiserver提供了资源操作的唯一入口,提供认证/授权/访问控制/api注册/发现等机制
- controller manager负责维护集群状态,例如故障检测/自动扩展/滚动更新等
- schedule负责资源的调度,按照预定的调度策略将pod调度到相应的机器上
- kubelet 维护容器的生命周期,同时负责volumn和net管理
- container runtime负责镜像管理以及pod和容器的真正运行
- kube-proxy负责为service提供cluster内部的服务发现和负载均衡



### 基础概念(自我理解版)

Pod: 是k8s中运行的最小单元，大部分情况代表一个容器，但是是比容器更上层的概念，可能一个Pod包含多个容器
Deployment: 代表部署管理一组Pod，管理Pod副本和生命周期，比如滚动更新和回滚
Service: 用来暴露Pod的网络服务，提供访问入口
Node: 代表工作节点，运行Pod的机器
Master: 控制节点，负责集群管理

### 常用命令

```shell
# 创建Service
kubectl expose deployment {podName} --port {port} -n {namespace} --type NodePort

# 查看
kubectl get pods -n {namespace} -o wide
kubectl get deployment -n {namespace}
kubectl get service -n {namespace}
kubectl describe pod {podName} -n {namespace}
kubectl describe deployment {deploymentName} -n {namespace}
kubectl describe service {serviceName} -n {namespace}

# 删除
kubectl delete service {serviceName} -n {namespace}
kubectl delete -f {file} -n {namespace}

# 回滚
# 查看部署状态
kubectl rollout status deployment {deploymentName} -n {namespace}
# 回滚
kubectl rollout undo deployment {deploymentName} -n {namespace}

# 修改部署pod数量
kubectl scale deployments {deploymentName} --replicas={replicasCount} -n {namespace}

```

