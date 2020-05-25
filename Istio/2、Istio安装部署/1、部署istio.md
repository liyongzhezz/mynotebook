**这里以istio-1.5.1为例**



## 下载istio



### 下载

下载 Istio，下载内容包含安装文件、示例和 [istioctl](https://istio.io/zh/docs/reference/commands/istioctl/) 命令行工具。

```shell
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.5.1
export PATH=$PWD/bin:$PATH
```



在这个目录下，包含以下文件：

- `install/kubernetes` ： Kubernetes 相关的 YAML 安装文件；
- `samples/` ：有示例应用程序；
- `bin/`：包含 [`istioctl`](https://istio.io/zh/docs/reference/commands/istioctl) 的客户端文件。`istioctl` 工具用于手动注入 Envoy sidecar 代理；



### 开启istio命令补全

`istioctl` 自动补全的文件位于 `tools` 目录。复制 `istioctl.bash` 文件到的 home 目录，然后添加内容到 `.bashrc` 文件即可：

```shell
cp /root/istio-1.5.1/tools/istioctl.bash /root/
echo "source ~/istioctl.bash" >> /root/.bashrc
```



## 安装istio

### 使用demo配置文件安装

```shell
istioctl manifest apply --set profile=demo
```



### 查看是否安装成功

检查所有svc是否具有ClusterIP（除jaeger外）：

```shell
kubectl get svc -n istio-system
```



查看pod是否处于运行状态：

```shell
kubectl get pods -n istio-system
```



## 注意事项

所有的应用程序需要遵循HTTP/1.1 或 HTTP/2.0 协议用于 HTTP 通信；HTTP/1.0 不支持。



### namespace设置

当使用 `kubectl apply` 来部署应用时，如果 pod 启动在标有 `istio-injection=enabled` 的命名空间中，那么，[Istio sidecar 注入器](https://istio.io/zh/docs/setup/additional-setup/sidecar-injection/#automatic-sidecar-injection)将自动注入 Envoy 容器到应用的 pod 中：

```shell
$ kubectl label namespace <namespace> istio-injection=enabled
$ kubectl create -n <namespace> -f <your-app-spec>.yaml
```



在没有 `istio-injection` 标记的命名空间中，在部署前可以使用 [`istioctl kube-inject`](https://istio.io/zh/docs/reference/commands/istioctl/#istioctl-kube-inject) 命令将 Envoy 容器手动注入到应用的 pod 中：

```shell
$ istioctl kube-inject -f <your-app-spec>.yaml | kubectl apply -f -
```



### pod和service要求

作为 Istio 服务网格中的一部分，Kubernetes 集群中的 Pod 和 Service 必须满足以下要求：

- **命名的服务端口**: Service 的端口必须命名。端口名键值对必须按以下格式：`name: <protocol>[-<suffix>]`。更多说明请参看[协议选择](https://istio.io/zh/docs/ops/configuration/traffic-management/protocol-selection/)。
- **Service 关联**: 每个 Pod 必须至少属于一个 Kubernetes Service，不管这个 Pod 是否对外暴露端口。如果一个 Pod 同时属于多个 [Kubernetes Service](https://kubernetes.io/docs/concepts/services-networking/service/)， 那么这些 Service 不能同时在一个端口号上使用不同的协议（比如：HTTP 和 TCP）。
- **带有 app 和 version 标签（label） 的 Deployment**: 我们建议显式地给 Deployment 加上 `app` 和 `version` 标签。给使用 Kubernetes `Deployment` 部署的 Pod 部署配置中增加这些标签，可以给 Istio 收集的指标和遥测信息中增加上下文信息。
  - `app` 标签：每个部署配置应该有一个不同的 `app` 标签并且该标签的值应该有一定意义。`app` label 用于在分布式追踪中添加上下文信息。
  - `version` 标签：这个标签用于在特定方式部署的应用中表示版本。
- **应用 UID**: 确保你的 Pod 不会以用户 ID（UID）为 1337 的用户运行应用。
- **`NET_ADMIN` 功能**: 如果你的集群执行 Pod 安全策略，必须给 Pod 配置 `NET_ADMIN` 功能。如果你使用 [Istio CNI 插件](https://istio.io/zh/docs/setup/additional-setup/cni/) 可以不配置。要了解更多 `NET_ADMIN` 功能的知识，请查看[所需的 Pod 功能](https://istio.io/zh/docs/ops/deployment/requirements/#required-pod-capabilities)。



## 卸载

```shell
istioctl manifest generate --set profile=demo | kubectl delete -f -
```

