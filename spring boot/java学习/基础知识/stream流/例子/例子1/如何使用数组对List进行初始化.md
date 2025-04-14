在Java中，您有几种方式可以使用数组初始化List：

## 方法1: 使用Arrays.asList()

<!-- replace lines 9 to 9 -->
```java
        List<Integer> a = Arrays.asList(1, 2, 3, 4, 5);
```

需要添加导入语句：

<!-- replace lines 1 to 4 -->
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static java.util.stream.Collectors.toList;
```

## 方法2: 使用List.of() (Java 9+)

<!-- replace lines 9 to 9 -->
```java
        List<Integer> a = List.of(1, 2, 3, 4, 5);
```

## 方法3: 从已有数组创建

<!-- replace lines 9 to 9 -->
```java
        Integer[] array = {1, 2, 3, 4, 5};
        List<Integer> a = new ArrayList<>(Arrays.asList(array));
```

注意：使用`Arrays.asList()`创建的List是固定大小的，如果需要可修改的List，建议使用`new ArrayList<>(Arrays.asList(...))`。