### Stream流的使用


1. 生成流
2. 中间操作
3. 终结操作

#### 生成Stream流

##### Collection体系集合

使用默认方法stream()生成流

```Java
List<String> list = new ArrayList<String>();
Stream<String> listStream = list.stream();
Set<String> set = new HashSet<String>();
Stream<String> setStream = set.stream();
```

##### Map体系集合

把Map转成Set集合，间接的生成流

```java
Map<String,Integer> map = new HashMap<String, Integer>();
Stream<String> keyStream = map.keySet().stream();
Stream<Integer> valueStream = map.values().stream();
Stream<Map.Entry<String, Integer>> entryStream = map.entrySet().stream();
```

##### 数组

通过Stream接口的静态方法of(T... values)生成流

```Java
String[] strArray = {"hello","world","java"};
Stream<String> strArrayStream = Stream.of(strArray);
Stream<String> strArrayStream2 = Stream.of("hello", "world", "java");
Stream<Integer> intStream = Stream.of(10, 20, 30);
```

#### Stream中间操作

执行完此方法之后，Stream流依然可以继续执行其他操作。

#### Stream终结操作

执行完此方法之后，Stream流将不能再执行其他操作。

#### Stream收集操作

对数据使用Stream流的方式操作完毕后，可以把流中的数据收集到集合中。

```Java
collect(Collectors.toList())//把元素收集到List集合中
collect(Collectors.toSet())//把元素收集到Set集合中
collect(Collectors.toMap())//把元素收集到Map集合
中
```

