### Lambda表达式

#### 格式

(形式参数) ->{代码块}

- 形式参数：如果有多个参数，参数之间用逗号隔开；如果没有参数，留空即可

#### 使用前提

1. 有一个接口
2. 接口中有且仅有一个方法

#### Lambda省略的规则

1. 参数类型可以省略。但是有多个参数的情况下，不能只省略一个
2. 如果参数有且仅有一个，那么小括号可以省略
3. 如果代码块的语句只有一条，可以省略大括号和分号，和return关键字

### 方法引用

#### 方法引用符

:: 该符号为引用运算符，而它所在的表达式被称为方法引用

#### lambda表达式支持的方法引用

##### 引用类方法

引用类方法就是引用类的静态方法

- 格式 类名::静态方法

Lambda表达式被类方法替代的时候，它的形式参数全部传递给静态方法作为参数

##### 引用对象的实例方法

引用对象的实例方法就是引用类中的成员方法

- 格式 对象::成员方法

Lambda表达式被对象的实例方法替代的时候，它的形式参数全部传递给该方法作为参数

##### 引用类的实例方法

引用类方法就是引用类类中的成员方法

- 格式 类名::成员方法

Lambda表达式被类的实例方法替代的时候 第一个参数作为调用者 后面的参数全部传递给该方法作为参数

##### 引用构造器

引用构造器就是引用构造方法

- 格式 类名::new

Lambda表达式被构造器替代的时候，它的形式参数全部传递给构造器作为参数

### 函数式接口

#### 概念:

有且仅有一个抽象方法的接口 (@FunctionalInterface 可以检测一个接口是不是函数式接口)

#### 函数式接口作为方法的参数

方法的参数是一个函数式接口,可以使用Lambda表达式作为参数传递

```Java
public class RunnableDemo {
    public static void main(String[] args) {
        startThread(() -> System.out.println(Thread.currentThread().getName() + "线程启动了"));
    }
    private static void startThread(Runnable r) {
        new Thread(r).start();
    }
}
```



#### 函数式接口作为方法的返回值

方法的返回值是一个函数时接口,可以使用Lambda表达式作为结果返回

```Java
public class ComparatorDemo {
    public static void main(String[] args) {
        ArrayList<String> array = new ArrayList<String>();
        array.add("cccc");
        array.add("aa");
        array.add("b");
        array.add("ddd");
        System.out.println("排序前：" + array);
        Collections.sort(array, getComparator());
        System.out.println("排序后：" + array);
    }
    private static Comparator<String> getComparator() {
        return (s1, s2) -> s1.length() - s2.length();
    }
}
```

#### 常用函数式接口

##### Supplier接口

生产型接口,只有一个无参的方法get();

##### Consumer接口

消费型接口

```java
public class ConsumerTest {
    public static void main(String[] args) {
        String[] strArray = {"em,30", "kb,35", "yz,33"};
        printInfo(strArray, str -> System.out.print("姓名：" + str.split(",")[0]),
                str -> System.out.println(",年龄：" +
                        Integer.parseInt(str.split(",")[1])));
    }
    private static void printInfo(String[] strArray, Consumer<String> con1,
                                  Consumer<String> con2) {
        for (String str : strArray) {
            con1.andThen(con2).accept(str);
        }
    }
}
```

##### Predicate接口

用于判断参数是否满足指定的条件

```
public class PredicateTest {
    public static void main(String[] args) {
        String[] strArray = {"qsh,55", "bq,34", "mt,35", "kb,31", "em,33"};
                ArrayList<String> array = myFilter(strArray, s -> s.split(",")[0].length() > 2,
                        s -> Integer.parseInt(s.split(",")[1]) > 33);
        for (String str : array) {
            System.out.println(str);
        }
}
        private static ArrayList<String> myFilter(String[] strArray, Predicate<String>pre1, Predicate<String> pre2) {
            ArrayList<String> array = new ArrayList<String>();
            for (String str : strArray) {
                if (pre1.and(pre2).test(str)) {
                    array.add(str);
                }
            }
            return array;
        }
    }
```

##### Function接口

用于对参数进行处理，转换(处理逻辑由Lambda表达式实现)，然后返回一个新的值

```Java
public class FunctionDemo {
    public static void main(String[] args) {
//操作一
        convert("100",s -> Integer.parseInt(s));
//操作二
        convert(100,i -> String.valueOf(i + 566));
//使用andThen的方式连续执行两个操作
        convert("100", s -> Integer.parseInt(s), i -> String.valueOf(i + 566));
    }
    //定义一个方法，把一个字符串转换int类型，在控制台输出
    private static void convert(String s, Function<String,Integer> fun) {
// Integer i = fun.apply(s);
        int i = fun.apply(s);
        System.out.println(i);
    }
    //定义一个方法，把一个int类型的数据加上一个整数之后，转为字符串在控制台输出
    private static void convert(int i, Function<Integer,String> fun) {
        String s = fun.apply(i);
        System.out.println(s);
    }
//定义一个方法，把一个字符串转换int类型，把int类型的数据加上一个整数之后，转为字符串在控制台输出
    private static void convert(String s, Function<String,Integer> fun1,
                                Function<Integer,String> fun2) {
        String ss = fun1.andThen(fun2).apply(s);
        System.out.println(ss);
    }
}
```

