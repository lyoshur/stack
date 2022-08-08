---
title: stream流操作
description: stream流操作
slug: stream流操作
date: 2022-03-10 00:00:00+0000
categories:
    - Java
tags:
    - JAVA
---

## stream流操作

stream位于java.util包中，是java8中新增类。

### 1.创建Stream

#### 1.1使用数组转为流

```java
String[] a = new String[3];
// 模拟设置值

Stream<String> ss = Stream.of(a);
long len = ss.count();
System.out.println(len);
```

#### 1.2使用数组转化为流

```java
String[] a = new String[3];
// 模拟设置值

Stream<String> ss = Arrays.stream(a);
long len = ss.count();
System.out.println(len);
```

#### 1.3使用List转化为流

```java
List<String> list = new ArrayList<>(10);

Stream<String> ss = list.stream();
System.out.println(ss.count());
```

### 2.使用流

#### 2.1筛选记录 filter

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("ab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream().filter(new Predicate<String>() {
    @Override
    public boolean test(String s) {
        return s.startsWith("a");
    }
});
System.out.println(ss.count());
```

可以使用 lambda 表达式，来简化代码。

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("ab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream().filter(s -> s.startsWith("a"));
System.out.println(ss.count());
```

#### 2.2处理记录 map

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("ab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream().map(new Function<String, String>() {
    @Override
    public String apply(String s) {
        return s + "-hahahaa";
    }
});
System.out.println(ss.collect(Collectors.joining(",")));
```

可以使用 lambda 表达式，来简化代码。

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("ab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream().map(s -> s + "-hahahah");
System.out.println(ss.collect(Collectors.joining(",")));
```

#### 2.3进行排序 sorted

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream().sorted();
System.out.println(ss.collect(Collectors.joining(",")));
```

#### 2.4合并流 flatMap

可以类似map，针对流中的每个元素进行处理，不同的是，返回值需要是一个另外的流。然后flatMap会将返回的所有流，进行合并。

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<Integer> ss = list.stream().flatMap(new Function<String, Stream<Integer>>() {
    @Override
    public Stream<Integer> apply(String s) {
        return Stream.of(1, 2, 3);
    }
});
System.out.println(ss.collect(Collectors.toList()));
```

可以使用 lambda 表达式，来简化代码。

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<Integer> ss = list.stream().flatMap(s -> Stream.of(1, 2, 3));
System.out.println(ss.collect(Collectors.toList()));
```

### 3.对流进行约简

#### 3.1收集为列表

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 重新将流收集成列表
List<String> data = ss.collect(Collectors.toList());

// 通过指定收集列表类型，来返回指定类型列表
// ArrayList
ArrayList<String> data1 = ss.collect(Collectors.toCollection(ArrayList::new));
// LinkedList
LinkedList<String> data2 = ss.collect(Collectors.toCollection(LinkedList::new));

System.out.println(data);
```

#### 3.2收集为Set

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 将流收集成Set
Set<String> data = ss.collect(Collectors.toSet());

System.out.println(data);
```

#### 3.3收集为Map

##### 3.3.1聚组Map

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 将流收集成Map
Map<String, List<String>> data = ss.collect(Collectors.groupingBy(String::toString));

System.out.println(data);
```

##### 3.3.2键值对Map

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 将流收集成Map
Map<String, String> data = ss.collect(Collectors.toMap(String::toString, String::toString));

System.out.println(data);
```

#### 3.4收集为String

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("bab");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 将流收集成String
String data = ss.collect(Collectors.joining("-"));

System.out.println(data);
```

#### 3.5 Collectors.collectingAndThen

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("a");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 首先将列表收集为Set进行去重，然后重新收集为列表
List<String> data = ss.collect(Collectors.collectingAndThen(Collectors.toSet(), ArrayList::new));

System.out.println(data);
```

### 4.其他约简操作

#### 4.1返回Optional<T>

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("a");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 查找列表第一个
Optional<String> data = ss.findFirst();

System.out.println(data.toString());
```

#### 4.2返回布尔值

```java
List<String> list = new ArrayList<>(10);
list.add("a");
list.add("a");
list.add("abc");
list.add("bcd");

Stream<String> ss = list.stream();

// 查找列表中，有没有 "a"
boolean data = ss.anyMatch(s -> s.equals("a"));

// 查找列表中，是不是全是 "a"
boolean data1 = ss.allMatch(s -> s.equals("a"));

System.out.println(data);
```