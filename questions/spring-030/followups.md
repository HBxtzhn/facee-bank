## 追问 1：你遇到过@Transactional失效吗？怎么解决的？

遇到过同类方法调用失效。createOrder内部调用updateInventory，updateInventory上有@Transactional但异常被catch吞掉没回滚。解决: 把updateInventory抽到独立InventoryService注入调用走代理，或catch中手动setRollbackOnly()。

## 追问 2：@Transactional注解可以加在接口上吗？

可以但不推荐。只有JDK动态代理时接口上注解才会生效。CGLIB代理(Spring Boot默认)下接口上事务注解不生效。最稳妥加在实现类的public方法上。
