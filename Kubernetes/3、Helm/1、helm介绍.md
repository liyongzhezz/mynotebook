helm是在kubernetes中对应用包进行管理的工具。



helm解决的问题：

1、对yaml文件的统一管理

2、yaml文件的高效复用

3、服务快速部署

4、应用的版本管理



组成：

helm：命令行工具

chart：yaml打包后的资源

release：版本，会记录每个chart的版本



helm v3的改变：

1、去掉了tiller组件，直接通过kubeconfig连接api-server完成部署；

2、release可以在不同namespace重用；

3、chart支持放到镜像仓库；