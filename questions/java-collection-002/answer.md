`fail-fast`（快速失败）和 `fail-safe`（安全失败）是 Java 集合框架在处理并发修改问题时，两种截然不同的设计哲学和容错策略。

关于 `fail-fast` 引用 `medium` 中一篇文章关于 `fail-fast` 和 `fail-safe` 的说法：

> Fail-fast systems are designed to immediately stop functioning upon encountering an unexpected condition. This immediate failure helps to catch errors early, making debugging more straightforward.

快速失败的思想即针对可能发生的异常进行提前表明故障并停止运行，通过尽早的发现和停止错误，降低故障系统级联的风险。

在 `java.util` 包下的大部分集合（如 `ArrayList`, `HashMap`）是不支持线程安全的，为了能够提前发现并发操作导致线程安全风险，提出通过维护一个 `modCount` 记录修改的次数，迭代期间通过比对预期修改次数 `expectedModCount` 和 `modCount` 是否一致来判断是否存在并发操作，从而实现快速失败，由此保证在避免在异常时执行非必要的复杂代码。

**ArrayList (fail-fast) 示例：**

```java
     // 使用线程不安全的 ArrayList，它是一种 fail-fast 集合
      List list = new ArrayList<>();
      CountDownLatch latch = new CountDownLatch(2);

      for (int i = 0; i < 5; i++) {
          list.add(i);
      }
      System.out.println("Initial list: " + list);

      Thread t1 = new Thread(() -> {
          try {
              for (Integer i : list) {
                  System.out.println("Iterator Thread (t1) sees: " + i);
                  Thread.sleep(100);
              }
          } catch (ConcurrentModificationException e) {
              System.err.println("!!! Iterator Thread (t1) caught ConcurrentModificationException as expected.");
          } catch (InterruptedException e) {
              e.printStackTrace();
          } finally {
              latch.countDown();
          }
      });

      Thread t2 = new Thread(() -> {
          try {
              Thread.sleep(50);
              System.out.println("-> Modifier Thread (t2) is removing element 1...");
              list.remove(Integer.valueOf(1));
              System.out.println("-> Modifier Thread (t2) finished removal.");
          } catch (InterruptedException e) {
              e.printStackTrace();
          } finally {
              latch.countDown();
          }
      });

      t1.start();
      t2.start();
      latch.await();

      System.out.println("Final list state: " + list);
```

输出：

```
Initial list: [0, 1, 2, 3, 4]
Iterator Thread (t1) sees: 0
-> Modifier Thread (t2) is removing element 1...
-> Modifier Thread (t2) finished removal.
!!! Iterator Thread (t1) caught ConcurrentModificationException as expected.
Final list state: [0, 2, 3, 4]
```

程序在线程 t2 修改列表后，线程 t1 的下一次迭代操作立刻就抛出了 `ConcurrentModificationException`。这是因为 ArrayList 的迭代器在每次 `next()` 调用时，都会检查 `modCount` 是否被改变。一旦发现集合在迭代器不知情的情况下被修改，它会立即“快速失败”，以防止在不一致的数据上继续操作导致不可预期的后果。

对此我们也给出 `for` 循环底层迭代器获取下一个元素时的 `next` 方法，可以看到其内部的 `checkForComodification` 具有针对修改次数比对的逻辑：

```java
 public E next() {
 			//检查是否存在并发修改
            checkForComodification();
            //......
            //返回下一个元素
            return (E) elementData[lastRet = i];
        }

final void checkForComodification() {
		//当前循环遍历次数和预期修改次数不一致时，就会抛出ConcurrentModificationException
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }

```

而 `fail-safe` 也就是安全失败的含义，它旨在即使面对意外情况也能恢复并继续运行，这使得它特别适用于不确定或者不稳定的环境：

> Fail-safe systems take a different approach, aiming to recover and continue even in the face of unexpected conditions. This makes them particularly suited for uncertain or volatile environments.

该思想常运用于并发容器，最经典的实现就是 `CopyOnWriteArrayList` 的实现，通过写时复制（Copy-On-Write）的思想保证在进行修改操作时复制出一份快照，基于这份快照完成添加或者删除操作后，将 `CopyOnWriteArrayList` 底层的数组引用指向这个新的数组空间，由此避免迭代时被并发修改所干扰所导致并发操作安全问题，当然这种做法也存在缺点，即进行遍历操作时无法获得实时结果：

对应我们也给出 `CopyOnWriteArrayList` 实现 `fail-safe` 的核心代码，可以看到它的实现就是通过 `getArray` 获取数组引用然后通过 `Arrays.copyOf` 得到一个数组的快照，基于这个快照完成添加操作后，修改底层 `array` 变量指向的引用地址由此完成写时复制：

```java
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
        	//获取原有数组
            Object[] elements = getArray();
            int len = elements.length;
            //基于原有数组复制出一份内存快照
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            //进行添加操作
            newElements[len] = e;
            //array指向新的数组
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```
