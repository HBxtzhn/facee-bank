## 追问 1：Namespace 和 Group 有什么区别？

Namespace 是最粗粒度的隔离，一般用于环境隔离（dev/test/prod）或项目隔离。Group 是中间粒度，一般用于区分同一个 Namespace 下的不同应用集群或不同业务线。DataId/ServiceName 是最细粒度，定位到具体的配置或服务。三层组合起来实现精确定位。

## 追问 2：你的项目里 Namespace 是怎么用的？

一般按环境划分四个 Namespace：dev、test、staging、prod。每个环境的服务注册和配置都在各自的 Namespace 下，互不干扰。开发环境的服务不会出现在生产环境的服务列表里。配置也是隔离的，dev 的数据库配置不会影响 prod。

## 追问 3：Nacos 的 Namespace 跟 K8s 的 Namespace 有什么关系？

没有直接关系，是两个不同层面的概念。Nacos Namespace 是应用层面的隔离，K8s Namespace 是基础设施层面的隔离。实际项目中，一个 K8s Namespace 里的服务通常对应一个 Nacos Namespace，但这不是强制的，只是约定俗成。
