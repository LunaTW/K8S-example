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

## Reference
- [Kubectl Install & Setting](https://kubernetes.io/zh/docs/tasks/tools/install-kubectl-macos/#install-kubectl-binary-with-curl-on-macos)
- [Minikube Install & Setting](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl Cheat Sheet](https://kubernetes.io/zh/docs/reference/kubectl/cheatsheet/)
