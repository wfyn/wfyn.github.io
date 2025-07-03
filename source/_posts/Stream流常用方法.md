---
title: Stream 流常用方法
date: 2024-5-26 17:51:36
tags: 
- Stream
- API
categories: java

---

她·

Stream常用的流方法以及它们的详细示例：

#### 1. `filter()`

用于过滤流中的元素，根据指定的条件保留符合条件的元素。

示例：

```java
javaList<String> names = Arrays.asList("John", "Alice", "Bob", "Emily");
List<String> filteredNames = names.stream()
                                 .filter(name -> name.startsWith("A"))
                                 .collect(Collectors.toList());
// 结果为 ["Alice"]
```

#### 2. `map()`

将流中的元素通过指定的函数映射为另一种类型。

示例：

```java
javaList<String> names = Arrays.asList("John", "Alice", "Bob", "Emily");
List<Integer> nameLengths = names.stream()
                                .map(String::length)
                                .collect(Collectors.toList());
// 结果为 [4, 5, 3, 5]
```

#### 3. `flatMap()`

将流中的每个元素转换为另一个流，并将所有流连接成一个流。

示例：

```java
javaList<List<Integer>> numbers = Arrays.asList(
    Arrays.asList(1, 2, 3),
    Arrays.asList(4, 5, 6),
    Arrays.asList(7, 8, 9)
);
List<Integer> flattenedList = numbers.stream()
                                    .flatMap(List::stream)
                                    .collect(Collectors.toList());
// 结果为 [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

#### 4. `sorted()`

对流中的元素进行排序。

示例：

```java
javaList<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9, 2, 6, 5);
List<Integer> sortedNumbers = numbers.stream()
                                    .sorted()
                                    .collect(Collectors.toList());
// 结果为 [1, 1, 2, 3, 4, 5, 5, 6, 9]
```

#### 5. `distinct()`

去除流中的重复元素。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 2, 4, 3, 5);
List<Integer> distinctNumbers = numbers.stream()
                                       .distinct()
                                       .collect(Collectors.toList());
// 结果为 [1, 2, 3, 4, 5]
```

#### 6. `limit()`

限制流中元素的数量。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
List<Integer> limitedNumbers = numbers.stream()
                                     .limit(5)
                                     .collect(Collectors.toList());
// 结果为 [1, 2, 3, 4, 5]
```

#### 7. `skip()`

跳过流中的前 N 个元素。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
List<Integer> skippedNumbers = numbers.stream()
                                      .skip(5)
                                      .collect(Collectors.toList());
// 结果为 [6, 7, 8, 9, 10]
```

#### 8. `forEach()`

对流中的每个元素执行指定的操作。

示例：

```java
javaList<String> names = Arrays.asList("John", "Alice", "Bob", "Emily");
names.stream()
     .forEach(System.out::println);
// 输出：
// John
// Alice
// Bob
// Emily
```

这些是流中的一些常用方法及其详细示例。您可以根据具体的需求使用这些方法来操作流中的元素。

#### 9. `anyMatch()`

检查流中是否至少有一个元素满足给定的条件。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
boolean anyMatch = numbers.stream()
                          .anyMatch(n -> n % 2 == 0);
// 结果为 true，因为存在偶数元素
```

#### 10. `allMatch()`

检查流中的所有元素是否都满足给定的条件。

示例：

```java
javaList<Integer> numbers = Arrays.asList(2, 4, 6, 8, 10);
boolean allMatch = numbers.stream()
                          .allMatch(n -> n % 2 == 0);
// 结果为 true，因为所有元素都是偶数
```

#### 11. `noneMatch()`

检查流中是否没有任何元素满足给定的条件。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 3, 5, 7, 9);
boolean noneMatch = numbers.stream()
                           .noneMatch(n -> n % 2 == 0);
// 结果为 true，因为没有任何元素是偶数
```

#### 12. `reduce()`

将流中的元素进行归约操作，得到一个最终的结果。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> sum = numbers.stream()
                              .reduce((x, y) -> x + y);
// 结果为 Optional[15]
```

#### 13. `collect()`

将流中的元素收集到一个集合中，如列表、集合或映射。

示例：

```java
javaList<String> names = Arrays.asList("John", "Alice", "Bob", "Emily");
Set<String> nameSet = names.stream()
                          .collect(Collectors.toSet());
// 结果为 {"John", "Alice", "Bob", "Emily"}
```

#### 14. `findFirst()`

获取流中的第一个元素（如果存在）。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> firstNumber = numbers.stream()
                                      .findFirst();
// 结果为 Optional[1]
```

#### 15. `findAny()`

获取流中的任意一个元素（如果存在），适用于并行流。

示例：

```java
javaList<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> anyNumber = numbers.stream()
                                    .findAny();
// 结果可能是 Optional[1]、Optional[2] 等
```