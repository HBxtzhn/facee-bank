OOP 不能很好地处理一些分散在多个类或对象中的公共行为（如日志记录、事务管理、权限控制、接口限流、接口幂等等），这些行为通常被称为 **横切关注点（cross-cutting concerns）** 。如果我们在每个类或对象中都重复实现这些行为，那么会导致代码的冗余、复杂和难以维护。

AOP 可以将横切关注点（如日志记录、事务管理、权限控制、接口限流、接口幂等等）从 **核心业务逻辑（core concerns，核心关注点）** 中分离出来，实现关注点的分离。

以日志记录为例进行介绍，假如我们需要对某些方法进行统一格式的日志记录，没有使用 AOP 技术之前，我们需要挨个写日志记录的逻辑代码，全是重复的逻辑。

```java
public CommonResponse method1() {
      // 业务逻辑
      xxService.method1();
      // 省略具体的业务处理逻辑
      // 日志记录
      ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
      HttpServletRequest request = attributes.getRequest();
      // 省略记录日志的具体逻辑 如：获取各种信息，写入数据库等操作...
      return CommonResponse.success();
}

public CommonResponse method2() {
      // 业务逻辑
      xxService.method2();
      // 省略具体的业务处理逻辑
      // 日志记录
      ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
      HttpServletRequest request = attributes.getRequest();
      // 省略记录日志的具体逻辑 如：获取各种信息，写入数据库等操作...
      return CommonResponse.success();
}

// ...
```

使用 AOP 技术之后，我们可以将日志记录的逻辑封装成一个切面，然后通过切入点和通知来指定在哪些方法需要执行日志记录的操作。
