AOP 的常见实现方式有动态代理、字节码操作等方式。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 CGLIB 生成一个被代理对象的子类来作为代理，如下图所示：

**Spring Boot 和 Spring 的动态代理的策略是不是也是一样的呢？**其实不一样，很多人都理解错了。

Spring Boot 2.0 之前，`spring.aop.proxy-target-class` 默认值为 `false`，有用户接口时通常使用 **JDK 动态代理**；如果目标类没有可用接口，Spring AOP 仍会回退到 **CGLIB 动态代理**，并不会仅仅因为目标类没有实现接口就抛出异常。Spring Boot 1.5.x 自动配置 AOP 代码如下：

```java
@Configuration
@ConditionalOnClass({ EnableAspectJAutoProxy.class, Aspect.class, Advice.class })
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = false)
 // 该配置类只有在 spring.aop.proxy-target-class=false 或未显式配置时才会生效。
 // 也就是说，如果开发者未明确选择代理方式，Spring 会默认加载 JDK 动态代理。
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false", matchIfMissing = true)
	public static class JdkDynamicAutoProxyConfiguration {

	}

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = true)
 // 该配置类只有在 spring.aop.proxy-target-class=true 时才会生效。
 // 即开发者通过属性配置明确指定使用 CGLIB 动态代理时，Spring 会加载这个配置类。
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "true", matchIfMissing = false)
	public static class CglibAutoProxyConfiguration {

	}

}
```

Spring Boot 2.0 开始，如果用户什么都不配置的话，默认使用 **CGLIB 动态代理**。如果需要强制使用 JDK 动态代理，可以在配置文件中添加：`spring.aop.proxy-target-class=false`。Spring Boot 2.0 自动配置 AOP 代码如下：

```java
@Configuration
@ConditionalOnClass({ EnableAspectJAutoProxy.class, Aspect.class, Advice.class,
		AnnotatedElement.class })
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = false)
 // 该配置类只有在 spring.aop.proxy-target-class=false 时才会生效。
 // 即开发者通过属性配置明确指定使用 JDK 动态代理时，Spring 会加载这个配置类。
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "false", matchIfMissing = false)
	public static class JdkDynamicAutoProxyConfiguration {

	}

	@Configuration
	@EnableAspectJAutoProxy(proxyTargetClass = true)
 // 该配置类只有在 spring.aop.proxy-target-class=true 或未显式配置时才会生效。
 // 也就是说，如果开发者未明确选择代理方式，Spring 会默认加载 CGLIB 代理。
	@ConditionalOnProperty(prefix = "spring.aop", name = "proxy-target-class", havingValue = "true", matchIfMissing = true)
	public static class CglibAutoProxyConfiguration {

	}

}
```

当然你也可以使用 **AspectJ** ！Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

**Spring AOP 属于运行时增强，AspectJ 支持编译时、后编译以及类加载时织入。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单。

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。
