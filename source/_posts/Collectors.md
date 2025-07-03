---
title: Collectors API 
date: 2024-5-26 17:42:51
tags: 
	- collectors
categories: java
top_img: /images/bg2.jpg

---



**Collectors** API 是 Java 中用于收集（collecting）流元素到各种数据结构的工具类。它提供了丰富的静态方法来支持将流元素收集到列表、集合、映射等不同类型的数据结构中。这些方法可以与流的 `collect` 方法一起使用，用于生成最终的结果。

下面是一些常用的 `Collectors` API 的用法示例：

### 1. `toList()`

将流中的元素收集到一个列表中。

示例：

```java
javaList<String> names = Stream.of("John", "Alice", "Bob", "Emily")
                           .collect(Collectors.toList());
// 结果为 ["John", "Alice", "Bob", "Emily"]
```

### 2. `toSet()`

将流中的元素收集到一个集合中。

示例：

```java
javaSet<String> uniqueNames = Stream.of("John", "Alice", "Bob", "Alice")
                                .collect(Collectors.toSet());
// 结果为 {"John", "Alice", "Bob"}
```

### 3. `toMap()`

将流中的元素收集到一个映射中，可以指定键和值的映射关系。

示例：

```java
javaMap<String, Integer> nameLengthMap = Stream.of("John", "Alice", "Bob", "Emily")
                                           .collect(Collectors.toMap(Function.identity(), String::length));
// 结果为 {"John"=4, "Alice"=5, "Bob"=3, "Emily"=5}
```

### 4. `joining()`

将流中的元素连接成一个字符串。

示例：

```java
javaString allNames = Stream.of("John", "Alice", "Bob", "Emily")
                        .collect(Collectors.joining(", "));
// 结果为 "John, Alice, Bob, Emily"
```

### 5. `groupingBy()`

根据指定的分类函数对流中的元素进行分组。

示例：

```java
javaMap<Integer, List<String>> namesByLength = Stream.of("John", "Alice", "Bob", "Emily")
                                                 .collect(Collectors.groupingBy(String::length));
// 结果为 {3=["Bob"], 4=["John"], 5=["Alice", "Emily"]}
```

### 6. `partitioningBy()`

根据给定的条件将流中的元素分成两组。

示例：

```java
javaMap<Boolean, List<String>> partitionedNames = Stream.of("John", "Alice", "Bob", "Emily")
                                         .collect(Collectors.partitioningBy(s -> s.length() > 3));
// 结果为 {false=["Bob"], true=["John", "Alice", "Emily"]}
```

### 7. `counting()`

计算流中元素的个数。

示例：

```java
javalong count = Stream.of("apple", "banana", "orange", "grape")
                   .collect(Collectors.counting());
// 结果为 4
```

### 8. `averagingInt()`

计算流中整数类型元素的平均值。

示例：

```java
javadouble averageLength = Stream.of("apple", "banana", "orange", "grape")
                             .collect(Collectors.averagingInt(String::length));
// 结果为 5.25 (所有单词长度之和除以单词个数)
```

### 9. `summarizingInt()`

对流中的整数类型元素进行汇总统计，包括计数、总和、平均值、最大值和最小值。

示例：

```java
javaIntSummaryStatistics statistics = Stream.of("apple", "banana", "orange", "grape")
                                       .collect(Collectors.summarizingInt(String::length));
// 结果为 IntSummaryStatistics{count=4, sum=21, min=5, average=5.250000, max=6}
```

### 10. `joining()`

将流中的元素连接成一个字符串，可以指定连接符、前缀和后缀。

示例：

```java
javaString concatenated = Stream.of("apple", "banana", "orange", "grape")
                            .collect(Collectors.joining(", ", "[", "]"));
// 结果为 "[apple, banana, orange, grape]"
```

### 11. `mapping()`

对流中的元素进行转换，并对转换结果进行收集。

示例：

```java
javaList<String> upperCaseNames = Stream.of("John", "Alice", "Bob", "Emily")
                                   .collect(Collectors.mapping(String::toUpperCase, Collectors.toList()));
// 结果为 ["JOHN", "ALICE", "BOB", "EMILY"]
```