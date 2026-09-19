1. **定义**：Namespace（命名空间）是 Nacos 中用于隔离不同环境或不同项目的最外层命名空间。不同 Namespace 之间的配置和服务是完全隔离的，互不影响。

2. **默认值**：Nacos 默认有一个 public 命名空间，如果不指定 Namespace，所有服务注册和配置都在 public 下。

3. **典型用法**：按环境隔离——创建 dev、test、staging、prod 四个 Namespace，每个环境的服务和配置互不干扰。也可以按项目隔离——项目A 和项目B 各一个 Namespace。

4. **三级定位**：Nacos 通过 Namespace + Group + DataId/ServiceName 三级定位唯一一份配置或服务。Namespace 是最粗粒度的隔离，Group 是中间粒度（默认 DEFAULT_GROUP），DataId/ServiceName 是最细粒度。

5. **配置方式**：在 ootstrap.yml 中配置 spring.cloud.nacos.discovery.namespace=xxx 和 spring.cloud.nacos.config.namespace=xxx。

## 扩展知识

- **Namespace 的 ID**：创建 Namespace 时会生成一个唯一的 ID（UUID），配置时用这个 ID 而不是名称。比如 spring.cloud.nacos.discovery.namespace=dev-namespace-id。
- **跨 Namespace 调用**：默认不同 Namespace 的服务不能互相发现。如果确实需要跨 Namespace 调用，可以通过自定义路由或者在 Feign URL 里直接指定目标服务地址。
- **权限控制**：Nacos 开源版不支持 Namespace 级别的权限控制（企业版支持）。可以通过部署多套 Nacos 集群来实现环境隔离。
