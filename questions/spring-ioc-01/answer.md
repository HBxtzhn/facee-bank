## 生命周期（简化）

1. 实例化（构造器 / 工厂方法）；
2. 属性填充（依赖注入）；
3. `Aware` 回调（`BeanNameAware`、`ApplicationContextAware` 等）；
4. `BeanPostProcessor.postProcessBeforeInitialization`；
5. `@PostConstruct` → `InitializingBean.afterPropertiesSet` → `init-method`；
6. `BeanPostProcessor.postProcessAfterInitialization`（**AOP 代理在此生成**）；
7. 使用；
8. 销毁：`@PreDestroy` → `DisposableBean.destroy` → `destroy-method`。

## 三级缓存

| 缓存 | 内容 | 作用 |
|---|---|---|
| `singletonObjects`（一级） | 成品 Bean | 正常获取 |
| `earlySingletonObjects`（二级） | 半成品（已实例化未填充） | 提前暴露 |
| `singletonFactories`（三级） | `ObjectFactory` | 延迟决定是否生成代理 |

流程：A 实例化后把工厂放进三级缓存 → 填充属性时发现需要 B → B 创建时又需要 A → 从三级缓存拿到工厂并调用 `getObject()`，**必要时在此提前生成 A 的代理**，放入二级缓存 → B 完成 → A 完成。

关键点：**三级缓存的真正意义是"延迟生成代理"**，保证循环依赖下注入的仍是代理对象而非原始对象。

## 无法解决的场景

- **构造器注入**的循环依赖（实例化阶段就卡住）⇒ 用 `@Lazy` 或改字段注入；
- **原型（prototype）** Bean 的循环依赖 ⇒ Spring 直接抛异常；
- `@Async` 与循环依赖叠加时容易报"BeanCurrentlyInCreationException"。
