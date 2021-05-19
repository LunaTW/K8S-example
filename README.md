# K8S-example
在本地部署一个 k8s 集群，了解k8s各种基础资源和基本操作命令。

Key word:
- Pod | Node | Kubernetes cluster
- Node
- deployment | service | ingress | cronjob
- MiniKube

## Workshop

### Prerequisites
- Kubectl => `brew install kubectl`
- Minikube => `brew install minikube`
- 验证 kubectl 配置 ==> `kubectl cluster-info` 如果返回一个 URL，则意味着 kubectl 成功的访问到了你的集群

### Basic: 

#### K8s 基本概念

![test image size](https://github.com/LunaTW/K8S-example/blob/master/ref/Pod.png?raw=true)
```
- Pod 
  Kubernetes 中创建和管理的、最小的可部署的计算单元

- Node
  Kubernetes 通过将容器放入在节点（Node）上运行的 Pod 中来执行你的工作负载。 
  节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。 
  每个节点包含运行 Pods 所需的服务； 这些节点由 控制面 负责管理。
  节点上的组件包括 kubelet、 docker runtime  以及 kube-proxy。

- Deployment
  Deployment 为 Pods 和 ReplicaSets 提供声明式的更新能力

- Service
  将运行在一组 Pods 上的应用程序公开为网络服务的抽象方法。
  Before：同一Node中，pod可以互相通信（通过IP地址）， 但由于同一个pod重启后IP地址会变，所以不方便
  Now：Service与IP绑定，和pod生命周期无关，IP不变了

- Ingress
  Ingress 是对集群中服务的外部访问进行管理的 API 对象，典型的访问方式是 HTTP。
  Ingress 可以提供负载均衡、SSL 终结和基于名称的虚拟托管。
  当 external service与外部通信时，希望暴露一个有意义且安全的url

- ReplicaSet
  ReplicaSet 的目的是维护一组在任何时候都处于运行状态的 Pod 副本的稳定集合。 
  因此，它通常用来保证给定数量的、完全相同的 Pod 的可用性

- Namespace
  Kubernetes 支持多个虚拟集群，它们底层依赖于同一个物理集群。 这些虚拟集群被称为名字空间 Namespace。
  名字空间适用于存在很多跨多个团队或项目的用户的场景。
  名字空间是在多个用户之间划分集群资源的一种方法，名字空间为名称提供了一个范围。资源的名称需要在名字空间内是唯一的。
```

#### K8s 基本命令 
```
- kubectl apply -f deployment.yaml      # 创建资源
- kubectl logs [-f] my-pod              # [流式]输出 Pod 的日志（标准输出）
- kubectl get services|pod|ingress|..   # 列出当前命名空间下的所有 services
- kubectl describe pods my-pod          # describe 命令的详细输出
- kubectl edit docker-registry          # 编辑名为 docker-registry 的服务
- kubectl port-forward my-pod 8080:8000 # 在本地计算机上侦听端口 8080 并转发到 my-pod 上的端口 8000
- kubectl delete pod,service baz foo    # 删除名称为 "baz" 和 "foo" 的 Pod 和服务
```

#### K8s Example
AC: 将 basic-express 中的 Node.js 应用通过 Deployment 部署，经过Service组织后由Ingress暴露出可被访问的API（使用kubectl apply）

Steps 1: 部署
- Image preparation
    ```
        1. docker build -t basic-express .                   # 生成 image
        2. docker tag ca0f16dc067a lunatw/node_k8s:latest    # 打 tag
        3. push image to docker hub
        4. got image id: lunatw/node_k8s
    ```
- minikube start 
    ```
        minikube start     # prepare k8s cluster env
    ```
- deploy
    ```
        kubectl apply -f deployment.yaml 
    ```
- service
    ```
        kubectl apply -f service.yaml
    ```
- ingress
    ```
        kubectl apply -f ingress.yaml
    ```

Step 2: 查看状态
- 使用 kubectl get查看本地运行的所有pod，deployment，service
    ```
        kubectl get pod           # 查看pod
        kubectl get service 
        kubectl get deployment
    ```


- 使用kubectl describe查看pod，deployment的详细信息
    ```
        kubectl describe pod
        kubectl describe deployment
    ```
- 使用 kubectl logs 查看正在运行的pod的日志
    ```
        kubectl logs -f node-app-deployment-59d45799f-l2wk5(e.g.)
  ```
- 使用 kubectl port-forward 命令将本地请求直接转发到 pod
    ```
        k port-forward node-app-deployment-59d45799f-l2wk5 8080:3000
  ```

Step 3: 对 deployment 扩容 / 收缩
- 通过 k8s dashboard
    ```
        minikube dashboard          # 启动 k8s dashboard
        - 选中左侧 deployment 
        - 通过 scale to Scale a resource
  ```

- 通过 kubectl scale 命令
    ```
        If the deployment named my-deployment and current size is 3, scale it to 4:
        kubectl scale [--current-replicas=3] --replicas=4 deployment/my-deployment
  ```

Step 4: k8s cronjob
- 创建 kubernetes cronjob
    ```
        k apply -f cronjob.yaml
  ```

#### K8s secrets Example
AC: 构建并部署一个带数据库的应用，使用 secrets 管理数据库密码，ConfigMap 管理其他配置信息

Step 1: Deploy Secret
- Apply the manifest:
    ```
        kubectl create -f mysql-secret.yaml
        kubectl get secrets                     # Verify that the Secret has just been created successfully
  ```
Step 2: Deploy MySQL  
- Apply the manifest file:
    ```
        kubectl create -f mysql-pod.yaml
        kubectl get pod                         # Verify that the pod is running
    ```
- Now, we can connect to the k8s-mysql pod:
    ```
        kubectl exec k8s-mysql -it -- bash
        ......(略)
  ```
- Apply the manifest to create the service:
    ```
        kubectl create -f mysql-service.yaml
        kubectl get svc                         # Verify that the service has been successfully created
  ```
Step 3: Creating a NodeJS Api to hit mysql
未完待续
.
.
.
.
.

## Reference
- [Kubectl Install & Setting](https://kubernetes.io/zh/docs/tasks/tools/install-kubectl-macos/#install-kubectl-binary-with-curl-on-macos)
- [Minikube Install & Setting](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl Cheat Sheet](https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/)
- [创建 Deployments](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/)
- [创建 Service](https://kubernetes.io/docs/concepts/services-networking/service/)
- [创建 Ingress](https://kubernetes.io/zh/docs/concepts/services-networking/ingress/)
- [Kubernetes的三种外部访问方式：NodePort、LoadBalancer 和 Ingress](http://dockone.io/article/4884)
- [Kubernetes Tutorial for Beginners [Video]](https://www.youtube.com/watch?v=X48VuDVv0do&t=210s)
- [How to Deploy MySQL on Kubernetes](https://linoxide.com/deploy-mysql-on-kubernetes/)