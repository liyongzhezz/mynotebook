## 什么是Istio

Istio是 ServiceMesh的一种实现方案，允许连接、保护、控制和观察微服务。Istio 有助于降低部署的复杂性，并减轻开发团队的压力。它是一个完全开源的服务网格，作为透明的一层接入到现有的分布式应用程序里。它也是一个平台，拥有可以集成任何日志、遥测和策略系统的 API 接口。



Istio 多样化的特性使您能够成功且高效地运行分布式微服务架构，并提供保护、连接和监控微服务的统一方法。

<br>



## Istio架构

<img src="static/istio.svg" style="zoom:67%;" />



Istio 服务网格从逻辑上分为数据平面和控制平面。

- **数据平面** 由一组智能代理（[Envoy](https://www.envoyproxy.io/)）组成，被部署为 sidecar。这些代理负责协调和控制微服务之间的所有网络通信。他们还收集和报告所有网格流量的遥测数据。
- **控制平面** 管理并配置代理来进行流量路由。



>  数据平面流量是指工作负载的业务逻辑发送和接收的消息。控制平面流量是指在 Istio 组件之间发送的配置和控制消息用来编排网格的行为。Istio 中的流量管理特指数据平面流量。



<br>



## 组件



### Envoy

Istio 使用 [Envoy](https://envoyproxy.github.io/envoy/) 代理的扩展版本。Envoy 是用 C++ 开发的高性能代理，用于协调服务网格中所有服务的入站和出站流量。***Envoy 代理是唯一与数据平面流量交互的 Istio 组件。***

Envoy 代理被部署为服务的 sidecar，在逻辑上为服务增加了 Envoy 的许多内置特性，例如:

- 动态服务发现
- 负载均衡
- TLS 终端
- HTTP/2 与 gRPC 代理
- 熔断器
- 健康检查
- 基于百分比流量分割的分阶段发布
- 故障注入
- 丰富的指标

<br>

这种 sidecar 部署允许 Istio 提取大量关于流量行为的信号作为[属性](https://istio.io/zh/docs/reference/config/policy-and-telemetry/mixer-overview/#attributes)。Istio 可以使用这些属性来实施策略决策，并将其发送到监视系统以提供有关整个网格行为的信息。

> sidecar 代理模型还允许向现有的部署添加 Istio 功能，而不需要重新设计架构或重写代码。

<br>

由 Envoy 代理启用的一些 Istio 的功能和任务包括:

- 流量控制功能：通过丰富的 HTTP、gRPC、WebSocket 和 TCP 流量路由规则来执行细粒度的流量控制。
- 网络弹性特性：重试设置、故障转移、熔断器和故障注入。
- 安全性和身份验证特性：执行安全性策略以及通过配置 API 定义的访问控制和速率限制。
- 基于 WebAssembly 的可插拔扩展模型，允许通过自定义策略实施和生成网格流量的遥测。



### Pilot

Pilot 为 Envoy sidecar 提供服务发现、用于智能路由的流量管理功能（例如，A/B 测试、金丝雀发布等）以及弹性功能（超时、重试、熔断器等）。

Pilot 将控制流量行为的高级路由规则转换为特定于环境的配置，并在运行时将它们传播到 sidecar。Pilot 将特定于平台的服务发现机制抽象出来，并将它们合成为任何符合 [Envoy API](https://www.envoyproxy.io/docs/envoy/latest/api/api) 的 sidecar 都可以使用的标准格式。

1. 平台启动一个服务的新实例，该实例通知其平台适配器。
2. 平台适配器使用 Pilot 抽象模型注册实例。
3. **Pilot** 将流量规则和配置派发给 Envoy 代理，来传达此次更改。

> 这种松耦合允许 Istio 在 Kubernetes、Consul 或 Nomad 等多种环境中运行，同时维护相同的 operator 接口来进行流量管理。



### Citadel

[Citadel](https://istio.io/zh/docs/concepts/security/) 通过内置的身份和证书管理，可以支持强大的服务到服务以及最终用户的身份验证。可以使用 Citadel 来升级服务网格中的未加密流量。使用 Citadel，operator 可以执行基于服务身份的策略，而不是相对不稳定的 3 层或 4 层网络标识。



### Galley

Galley 是 Istio 的配置验证、提取、处理和分发组件。它负责将其余的 Istio 组件与从底层平台（例如 Kubernetes）获取用户配置的细节隔离开来。



<br>



## Istio解决的问题

- 故障排查：
  - 请求在哪里失败了？A有调用B吗？
  - 为什么请求hang住了？
  - 为什么系统响应慢？哪个组件慢？
- 应用容错：
  - 客户端没有设置timeout导致卡住；
  - 没有重试机制，某个服务出错直接返回用户错误页面；
  - 某些节点异常导致响应变长；
- 应用升级发布：
  - 新版本一次性升级，出错影响范围大；
  - 无法进行A/B测试，根据用户属性访问不同版本；
  - 服务版本处理不当导致服务不可用；
- 系统安全：
  - 服务都是http而非https；
  - 没有流量限制，任何人都可以对服务发起攻击；