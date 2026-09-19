- **DTO**(Data Transfer Object): Controller接收请求参数，加@Valid校验注解
- **VO**(View Object): 返回给前端，只含前端需要字段，密码/内部状态不暴露
- **PO**(Persistent Object): 数据库实体，和表结构一一映射

分层好处: 1) 职责分离——改表结构只变PO不变接口，前端需求变只改VO; 2) 安全——不会JSON序列化不小心暴露敏感字段; 3) 字段级精准控制——同一PO不同接口VO可不同(列表精简VO详情完整VO)。
