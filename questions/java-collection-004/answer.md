`BlockingQueue`（阻塞队列）是一个接口，继承自 `Queue`。它为插入和移除操作分别提供了抛出异常、返回特殊值、持续阻塞和超时等待四种处理方式。其中，`take()` 可以在队列为空时阻塞，`put()` 可以在容量受限的队列已满时阻塞。

```java
public interface BlockingQueue extends Queue {
  // ...
}
```

`BlockingQueue` 常用于生产者-消费者模型中，生产者线程会向队列中添加数据，而消费者线程会从队列中取出数据进行处理。
