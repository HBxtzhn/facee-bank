JDK 21 中常见的创建方式有四种。

### 使用 `Thread.startVirtualThread()`

适合启动一个很简单的虚拟线程：

```java
public class VirtualThreadDemo {
  public static void main(String[] args) throws InterruptedException {
    Thread thread = Thread.startVirtualThread(() -> {
      System.out.println(Thread.currentThread());
    });

    thread.join();
  }
}
```

需要注意的是，虚拟线程是守护线程。如果 `main` 方法不等待它结束，JVM 可能直接退出，导致任务还没来得及执行完。

### 使用 `Thread.ofVirtual()`

`Thread.ofVirtual()` 返回一个 `Thread.Builder.OfVirtual`，可以设置线程名，也可以选择创建后立即启动或先不启动：

```java
public class VirtualThreadDemo {
  public static void main(String[] args) throws InterruptedException {
    Thread unstarted = Thread.ofVirtual()
        .name("order-query")
        .unstarted(() -> System.out.println("query order"));

    unstarted.start();
    unstarted.join();

    Thread started = Thread.ofVirtual()
        .name("payment-query")
        .start(() -> System.out.println("query payment"));

    started.join();
  }
}
```

### 使用 `ThreadFactory`

如果你希望统一线程命名，或者把线程工厂交给框架使用，可以通过 `ThreadFactory` 创建虚拟线程：

```java
import java.util.concurrent.ThreadFactory;

public class VirtualThreadDemo {
  public static void main(String[] args) throws InterruptedException {
    ThreadFactory factory = Thread.ofVirtual()
        .name("worker-", 0)
        .factory();

    Thread thread = factory.newThread(() -> {
      System.out.println(Thread.currentThread().getName());
    });

    thread.start();
    thread.join();
  }
}
```

### 使用 `Executors.newVirtualThreadPerTaskExecutor()`

业务开发中最常见的是这种方式。它会为每个提交的任务创建一个新的虚拟线程：

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class VirtualThreadDemo {
  public static void main(String[] args) throws Exception {
    try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
      Future future = executor.submit(() -> {
        return "hello virtual thread";
      });

      System.out.println(future.get());
    }
  }
}
```

这里的 `ExecutorService` 不是传统意义上的线程池。它不会维护一组固定虚拟线程来复用，而是每个任务一个新的虚拟线程。`try-with-resources` 结束时会调用 `close()`，等待已提交任务完成。
