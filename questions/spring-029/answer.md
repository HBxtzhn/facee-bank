7种: REQUIRED(默认，加入事务或新建)、REQUIRES_NEW(始终新建挂起当前)、SUPPORTS(有就加入没有就非事务)、NOT_SUPPORTED(非事务挂起当前)、MANDATORY(必须事务否则抛异常)、NEVER(必须非事务否则抛异常)、NESTED(嵌套通过savepoint部分回滚)。

最常用: REQUIRED和REQUIRES_NEW(如日志记录不应随业务回滚)。
