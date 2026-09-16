上例中 `Optional.ofNullable` 是其中一种创建 Optional 的方式。我们先看一下它的含义和其他创建 Optional 的源码方法。

```java
/**
* Common instance for {@code empty()}. 全局EMPTY对象
*/
private static final Optional<?> EMPTY = new Optional<>();

/**
* Optional维护的值
*/
private final T value;

/**
* 如果value是null就返回EMPTY，否则就返回of(T)
*/
public static  Optional ofNullable(T value) {
   return value == null ? empty() : of(value);
}
/**
* 返回 EMPTY 对象
*/
public static Optional empty() {
   Optional t = (Optional) EMPTY;
   return t;
}
/**
* 返回Optional对象
*/
public static  Optional of(T value) {
    return new Optional<>(value);
}
/**
* 私有构造方法，给value赋值
*/
private Optional(T value) {
  this.value = Objects.requireNonNull(value);
}
/**
* 所以如果of(T value) 的value是null，会抛出NullPointerException异常，这样貌似就没处理NPE问题
*/
public static  T requireNonNull(T obj) {
  if (obj == null)
         throw new NullPointerException();
  return obj;
}
```

`ofNullable` 方法和 `of` 方法的主要区别是：当 value 为 `null` 时，`ofNullable` 返回空 Optional，而 `of` 会抛出 `NullPointerException`。当 `null` 表示合法的“没有值”时使用 `ofNullable`；当参数按约定必须非 `null` 时可以使用 `of` 及早暴露错误。

**`map()` 和 `flatMap()` 有什么区别的？**

`map` 和 `flatMap` 都是将一个函数应用于集合中的每个元素，但不同的是 `map` 返回一个新的集合，`flatMap` 是将每个元素都映射为一个集合，最后再将这个集合展平。

在实际应用场景中，如果 `map` 返回的是数组，那么最后得到的是一个二维数组，使用 `flatMap` 就是为了将这个二维数组展平变成一个一维数组。

```java
public class MapAndFlatMapExample {
    public static void main(String[] args) {
        List listOfArrays = Arrays.asList(
                new String[]{"apple", "banana", "cherry"},
                new String[]{"orange", "grape", "pear"},
                new String[]{"kiwi", "melon", "pineapple"}
        );

        List mapResult = listOfArrays.stream()
                .map(array -> Arrays.stream(array).map(String::toUpperCase).toArray(String[]::new))
                .collect(Collectors.toList());

        System.out.println("Using map:");
        mapResult.forEach(arrays-> System.out.println(Arrays.toString(arrays)));

        List flatMapResult = listOfArrays.stream()
                .flatMap(array -> Arrays.stream(array).map(String::toUpperCase))
                .collect(Collectors.toList());

        System.out.println("Using flatMap:");
        System.out.println(flatMapResult);
    }
}

```

运行结果:

```plain
Using map:
[[APPLE, BANANA, CHERRY], [ORANGE, GRAPE, PEAR], [KIWI, MELON, PINEAPPLE]]

Using flatMap:
[APPLE, BANANA, CHERRY, ORANGE, GRAPE, PEAR, KIWI, MELON, PINEAPPLE]
```

最简单的理解就是 `flatMap()` 可以将 `map()` 的结果展开。

在 `Optional` 里面，当使用 `map()` 时，如果映射函数返回的是一个普通值，它会将这个值包装在一个新的 `Optional` 中。而使用 `flatMap` 时，如果映射函数返回的是一个 `Optional`，它会将这个返回的 `Optional` 展平，不再包装成嵌套的 `Optional`。

下面是一个对比的示例代码：

```java
public static void main(String[] args) {
        int userId = 1;

        // 使用flatMap的代码
        String cityUsingFlatMap = getUserById(userId)
                .flatMap(OptionalExample::getAddressByUser)
                .map(Address::getCity)
                .orElse("Unknown");

        System.out.println("User's city using flatMap: " + cityUsingFlatMap);

        // 不使用flatMap的代码
        Optional> optionalAddress = getUserById(userId)
                .map(OptionalExample::getAddressByUser);

        String cityWithoutFlatMap;
        if (optionalAddress.isPresent()) {
            Optional addressOptional = optionalAddress.get();
            if (addressOptional.isPresent()) {
                Address address = addressOptional.get();
                cityWithoutFlatMap = address.getCity();
            } else {
                cityWithoutFlatMap = "Unknown";
            }
        } else {
            cityWithoutFlatMap = "Unknown";
        }

        System.out.println("User's city without flatMap: " + cityWithoutFlatMap);
    }
```

在 `Stream` 和 `Optional` 中正确使用 `flatMap` 可以减少很多不必要的代码。
