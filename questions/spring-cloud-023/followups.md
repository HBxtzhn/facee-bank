## 追问 1：Consul 的健康检查有哪些方式？

四种：Script（执行一个脚本，返回0表示健康）、HTTP（定时请求一个URL，200表示健康）、TCP（定时检测端口是否可连接）、TTL（客户端在指定时间内主动汇报存活状态，类似心跳）。

## 追问 2：Consul 和 Nacos 怎么选？

国内项目首选 Nacos，中文文档好，社区活跃，功能更全（注册中心+配置中心一体）。如果团队已经用 HashiCorp 生态（Terraform、Vault），或者需要多数据中心部署，Consul 更合适。Consul 是 CP 模型，强一致但 Leader 选举期间不可用；Nacos 默认 AP，可用性更好。

## 追问 3：Consul 的 KV 存储能当配置中心用吗？

可以，Consul 的 KV 存储支持版本管理、Watch 机制（配置变更通知），可以当配置中心用。但功能比较原始，没有 Nacos Config 和 Apollo 那样完善的管理界面、灰度发布、权限管理。一般用 Consul 的项目，配置中心也会单独选 Nacos 或 Apollo。
