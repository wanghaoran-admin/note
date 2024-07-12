# 第一章 Stream概述

### 1.概述

Java 8 是一个非常成功的版本，这个版本新增的`Stream`，配合同版本出现的 `Lambda` ，给我们操作集合（Collection）提供了极大的便利。



### 2.那么什么是`Stream`？

`Stream`将要处理的元素集合看作一种流，在流的过程中，借助`Stream API`对流中的元素进行操作，比如：筛选、排序、聚合等。



`Stream`可以由数组或集合创建，对流的操作分为两种：

1. 中间操作，每次返回一个新的流，可以有多个。
2. 终端操作，每个流只能进行一次终端操作，终端操作结束后流无法再次使用。终端操作会产生一个新的集合或值。



### 3.stream特性

`Stream`有几个特性：

1. stream不存储数据，而是按照特定的规则对数据进行计算，一般会输出结果。
2. stream不会改变数据源，通常情况下会产生一个新的集合或一个值。
3. stream具有延迟执行特性，只有调用终端操作时，中间操作才会执行。



# 第二章 Stream的创建

### 1.Stream可以通过集合数组创建

```java
// 从集合创建
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> streamFromList = list.stream();

// 从数组创建
String[] array = {"a", "b", "c"};
Stream<String> streamFromArray = Arrays.stream(array);

// 直接创建
Stream<String> streamOf = Stream.of("a", "b", "c");
```







# 第三章 stream和parallelStream

### 1.**stream和parallelStream的简单区分**

`stream`是顺序流，由主线程按顺序对流执行操作，而`parallelStream`是并行流，内部以多线程并行执行的方式对流进行操作，但前提是流中的数据处理没有顺序要求。例如筛选集合中的奇数，两者的处理不同之处：

![](https://img-blog.csdnimg.cn/20201106164400889.png)





### 2.parallel()

除了直接创建并行流，还可以通过`parallel()`把顺序流转换成并行流：

```java
Optional<Integer> findFirst = list.stream().parallel().filter(x->x>6).findFirst();
```







# 第四章 Stream常用方法

## **1.中间操作**

用于构建流水线，对数据流进行处理和转换，支持链式调用，不会立即执行。



### **filter**

过滤流中的元素。

```java
java复制代码List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> evenNumbers = numbers.stream()
                                   .filter(n -> n % 2 == 0)
                                   .collect(Collectors.toList());
// evenNumbers = [2, 4]
```





### **map**

将流中的每个元素映射到另一个对象。

```java
java复制代码List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
List<Integer> nameLengths = names.stream()
                                 .map(String::length)
                                 .collect(Collectors.toList());
// nameLengths = [5, 3, 7]
```





### **flatMap**

将流中的每个元素映射为一个流，并将这些流连接为一个流。

```java
java复制代码List<List<Integer>> nestedNumbers = Arrays.asList(
    Arrays.asList(1, 2),
    Arrays.asList(3, 4),
    Arrays.asList(5, 6)
);
List<Integer> flatNumbers = nestedNumbers.stream()
                                         .flatMap(List::stream)
                                         .collect(Collectors.toList());
// flatNumbers = [1, 2, 3, 4, 5, 6]
```





### **distinct**

去除流中重复的元素。

```java
java复制代码List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 4, 5);
List<Integer> distinctNumbers = numbers.stream()
                                       .distinct()
                                       .collect(Collectors.toList());
// distinctNumbers = [1, 2, 3, 4, 5]
```





### **sorted**

对流中的元素进行排序。

```java
java复制代码List<Integer> numbers = Arrays.asList(5, 3, 8, 1, 4);
List<Integer> sortedNumbers = numbers.stream()
                                     .sorted()
                                     .collect(Collectors.toList());
// sortedNumbers = [1, 3, 4, 5, 8]
```





### **limit** 和 **skip**

截取流中的元素。

```java
java复制代码List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> limitedNumbers = numbers.stream()
                                      .limit(3)
                                      .collect(Collectors.toList());
// limitedNumbers = [1, 2, 3]

List<Integer> skippedNumbers = numbers.stream()
                                      .skip(2)
                                      .collect(Collectors.toList());
// skippedNumbers = [3, 4, 5]
```





## 2.终端操作

用于触发流的处理和计算，返回最终的结果，触发执行。



### **forEach**

对流中的每个元素执行操作。

```java
java复制代码List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.stream()
     .forEach(System.out::println);
// 输出：
// Alice
// Bob
// Charlie
```





### **collect**

将流中的元素收集到一个集合或其他数据结构中。

```java
java复制代码List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
List<String> collectedNames = names.stream()
                                  .filter(name -> name.startsWith("A"))
                                  .collect(Collectors.toList());
// collectedNames = ["Alice"]
```





### **reduce**

通过指定的函数将流中的元素归约为一个值。

```java
java复制代码List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> sum = numbers.stream()
                               .reduce(Integer::sum);
// sum = 15
```





### **count**

返回流中的元素数量。

```java
java复制代码List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
long count = names.stream()
                  .filter(name -> name.length() > 4)
                  .count();
// count = 1
```





### **anyMatch**、**allMatch**、**noneMatch**

检查流中的元素是否满足给定的条件。

```java
java复制代码List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
boolean anyMatch = numbers.stream().anyMatch(n -> n > 3);  // true
boolean allMatch = numbers.stream().allMatch(n -> n > 0);   // true
boolean noneMatch = numbers.stream().noneMatch(n -> n < 0); // true
```





### **findFirst** 和 **findAny**

查找流中的第一个元素或任意一个元素。

```java
java复制代码List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
Optional<String> first = names.stream().findFirst();
Optional<String> any = names.stream().findAny();
```
