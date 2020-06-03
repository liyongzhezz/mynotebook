这里使用的是3.2.1版本。



## 下载安装包

```bash
wget https://get.helm.sh/helm-v3.2.1-linux-amd64.tar.gz
```

<br>

## 安装

```bash
tar zxf helm-v3.2.1-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
```

<br>



## 验证

```bash
helm version
```



默认使用的是`/root/.kube/confg`作为连接集群的kubeconfig，如果文件在其他地方则使用`--kubeconfig <file-path>`来指定。

<br>



## helm命令补全

```bash
yum install -y bash-completion
source /usr/share/bash-completion/bash_completion
source <(helm completion bash)
```

<br>



## 添加chart源

使用下面的命令查看当前的chart源：

```bash
helm repo list
```



默认没有任何源信息，使用下面的命令添加微软源：（也可以添加其他的源）

```bash
helm repo add stable http://mirror.azure.cn/kubernetes/charts
```



<br>



## 搜索chart

使用下面的命令可以在cahrt源总搜索想要安装的包：

```bash
# 这里以weave项目为例
helm search repo weave
```

