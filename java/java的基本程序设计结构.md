### 1 代码解读

![image-20210724170254840](https://i.loli.net/2021/08/07/mGQLWY2KjdEarkb.png)

### 2 数据类型

#### 2.1 整形:

##### 2.1.1 long:

长整型数值有一个后缀L或l

![image-20210724172246558](https://i.loli.net/2021/08/07/dyNaHAkITnPvWjt.png)

#### 2.2 浮点类型

##### 2.1.2double:

![image-20210724172431206](https://i.loli.net/2021/08/07/QSq4GYVwUIRPxDf.png)

##### 2.1.3float:

float类型的数值有一个后缀F或f

![image-20210724173032584](https://i.loli.net/2021/08/07/sASXK5pj7dQzxqk.png)

###### 代码:

```java
public class day01 {
    public static void main(String[] args) {
        long a = 100000000000L;
        long b = 100000000000;
        long c = 100;
        //long类型数值超过int最大数值时会报错必须加上L;
        double a1 = 100000000000D;
        double b1 = 100000000000;
        double c1 = 100;
        double d1 = 100000000000.000;
        //double类型数值无小数点超过int最大数值时会报错必须加上D;
        float a2 = 100000000000f;
        float b2 = 100000000000;
        float c2 = 100000000000.000;
        float d2 = 100000000000.000f;
        float e2 = 100;
        float f2 = 100.00;
        //float类型数值无小数点超过int最大数值时会报错必须加上F;
        //float类型数值有小数点时不加F则后面数值为double类型,类型不匹配会报错;
    }
}
```

##### 2.1.4特殊浮点数值NaN:

NaN(不是一个数字)

```
public class day01 {
    public static void main(String[] args) {
        double d = 100.00;
        if (Double.isNaN(d)) {
            System.out.println("d不是一个数字");
        } else {
            System.out.println("d是一个数字");
        }
    }
}
```

![image-20210724174454936](https://i.loli.net/2021/08/07/bFDUcQ5ZVSA9CJL.png)

#### 2.3 常量:

关键字final表示这个变量只能被赋值一次

![image-20210724174816960](https://i.loli.net/2021/08/07/7tUZ6lOTJP4VwMR.png)

#### 2.4 类常量:

用关键字static final设置类常量

用法:希望某个常量可以在一个类的多个方法中使用(类常量的定义位于main方法的外部)

```java
public class note {
    public  static final double a=100.01;
    public static void main(String[] args) {
        System.out.println(a);
        note note = new note();
        double add = note.add(a, 100.00);
        System.out.println(add);
    }

    public static double add(double a, double b) {
        return a + b;
    }
}
```

![image-20210724181326233](https://i.loli.net/2021/08/07/cN791S32TU58Hfe.png)

#### 2.5 枚举类型

**枚举是一个特殊的类**

不能设置在方法中:

![image-20210724183036975](https://i.loli.net/2021/08/07/SQ2iCozKUFRLdTk.png)

使用:

```
public class note {
    enum size{minimum , middle,maximum};
    public static void main(String[] args) {
     size s = size.maximum;
     size s1 = null;
        System.out.println(s);
        System.out.println(s1);
    }
}
```

结果:

![image-20210724183401593](https://i.loli.net/2021/08/07/V4O3XlrBZS5D2gL.png)

### 3 数据类型之间的转换

![image-20210728105902105](https://i.loli.net/2021/08/07/U68orD5dbfjVsMJ.png)

#### 3.1 强制类型转换

3.1.1 基本类型:

![image-20210728113132231](https://i.loli.net/2021/08/07/s3UvMPDKbxtaqTA.png)

3.1.2 结合赋值和运算符:

![image-20210728113625616](https://i.loli.net/2021/08/07/qTSF73bW8RBntNg.png)

3.1.3 自动转换:

![image-20210728114006414](https://i.loli.net/2021/08/07/eWhD1KS8XYMj2vU.png)



如果两个操作数中有一个是double类型,另一个操作数就会转换为double类型.

否则,如果其中一个操作数是float类型,另一个操作数就会转换为float类型.

否则,如果其中一个操作数是long类型,另一个操作数就会转换为long类型.

否则,两个操作数都将会被转换为int类型.

![image-20210728115222494](https://i.loli.net/2021/08/07/3GcT217bSxyYHuE.png)

#### 3.2 boolean运算符:

&&和||运算符是按照"短路"方式来求值的:如果一个操作数已经能确定表达式的值了,第二个操作数就不必计算了.

&和|不采用"短路"方式来求值.

##### 3.2.1 三目运算符:

condition ?  true : false

```java
public class note {
    public static void main(String[] args) {
      int a = 1;
      int b = 0;
        System.out.println(5 < 6 ? a : b);
        System.out.println(6>1?10:20);
    }
}
```

![image-20210728120142511](https://i.loli.net/2021/08/07/yVsF27INdQR9YO8.png)

#### 3.3位运算符:

##### 左移:

![image-20210728121100463](https://i.loli.net/2021/08/07/Lur3GJSZBsm6bjC.png)

![image-20210728121220218](https://i.loli.net/2021/08/07/j4XU3tqnY7ZWgop.png)

上图因为是short(2^15)所以共为16格

每左移一位则结果*2(符号位不变时)

##### 右移:

![image-20210728132917906](https://i.loli.net/2021/08/07/CPY7RMbufG6adHj.png)

32768为int(2^31)变量所以有32格此处省略了16格

![image-20210728133125881](https://i.loli.net/2021/08/07/vX2CxIlOAQqUcMJ.png)

![image-20210728133210045](https://i.loli.net/2021/08/07/ul6DNgZSbKMhI7C.png)

##### 无符号右移

![image-20210728133404565](https://i.loli.net/2021/08/07/wsJf2tX58WzkQea.png)

不考虑正负数最高位一律补零(第一次右移和初始值无联系,第二次右移是第一次右移的一半)

byte,short是低精度的不适合做>>>(若使用会导致结果不准确)

#### 4 字符串:

##### 4.1 String特点:

1.字符串内容永不可变。

2.字符串可以共享使用。

3.字符串效果相当于char[]字符数组，但是底层原理都是byte[]字节数组。

##### 4.2 构造方式:

```java
public class note {
    public static void main(String[] args) {
        //1.无参构造
        String s = new String();

        // 2.通过字符数组构造
        char[] chars = {'s', 't', 'r', 'i', 'n', 'g'};
        String s1 = new String(chars);

        //3.通过字节数组构造
        byte[] bytes = {96, 97, 98};
        String s2 = new String(bytes);

        //4.直接构造
        String s3 = "String";
        
        //1,2,3中创建字符串对象每次都会申请一个内存空间虽然内容相同,但地址值是不同的
        //4中只要字符序列相同(顺序和大小写),无论代码中出现几次JVM都只会建立一个String对象,并在字符串池中维护
    }
}
```

#### 5 构建字符串:

##### 5.1 StringBuilder:

特点:

1. 避免拼接字符串都会构建一个新的String对象浪费时间和空间
2. 单线程的方式执行操作(线程安全)
3. 效率低

##### 5.2 StringBuffer

特点:

1. 避免拼接字符串都会构建一个新的String对象浪费时间和空间
2. 多线程的方式执行操作(线程不安全)
3. 效率高

### 6 格式化输出:

```
public class note {
    public static void main(String[] args) {
        double x = 10000.0 / 3.0;
        System.out.println(x);
        System.out.printf("%8.2f", x);
      
    }
}
```

![image-20210728144513291](https://i.loli.net/2021/08/07/wOY4GAMjuhyJ1Vq.png)

("8.2f"表示包含8个字符,精确为小数点后两个字符,以上面代码为例会打印一个前导空格和七个字符)

字符数不够最前面补空格

1. f:表示浮点数
2. s:表示字符串
3. d表示十进制整数

### 7 控制流程

#### 7.1 块作用域:

一对大括号括起来为块,块确定了变量的作用域,一个块可以嵌套在另外一个块中,但不能在嵌套的两个块中声明同名的变量

```
public class note {
    public static void main(String[] args) {
        {
            int a = 10;
            System.out.println(a);
        }
        {
            int a = 20;
            System.out.println(a);
        }
    }
}
```

![image-20210728145615024](https://i.loli.net/2021/08/07/VDXI4x8hguG9NdU.png)

#### 7.2 条件语句

##### 7.2.1 while循环

```java
while( 布尔表达式 ) {
    //循环内容
}
```

##### 7.2.2 do…while 循环

```java
do {
    //代码语句
}while(布尔表达式);
```

##### 7.2.3 for循环

```java
for(初始化; 布尔表达式; 更新) {
    //代码语句
}
```

##### 7.2.4 Java 增强 for 循环

```java
for(声明语句 : 表达式) {
	//代码句子
}
```

##### 7.2.5 多重选择 switch

```java
switch(表达式){
        case 常量表达式1:  语句1;
        case 常量表达式2:  语句2;
		…
        case 常量表达式n:  语句n;
        default:  语句n+1;
    }
```

case:标签可以是:

1. 类型为char,short,int的常量表达式
2. 枚举长量
3. 字符串字面量

当在switch中使用枚举常量时不必在每个标签中指明举名,可以由switch的表达式值推导得出:

##### 7.2.6 break

不带标签的break跳出当前循环

带标签的break跳出到带标签的语句块末尾

```java
标签:
循环{
break 标签;
}
```

##### 7.2.7 continue

中断正常的控制流程,将控制转移到最内层循环的首部

### 8 大数:

```Java
//      将普通的数转换为大数
        BigInteger bigInteger = BigInteger.valueOf(1000);
//        用于更大的数,将其转换为大数
        BigInteger bigInteger1 = new BigInteger("111111111111111111111111111111111111");
```

### 9 数组:

#### 9.1 声明数组变量:

```java
dataType[] arrayRefVar;   // 首选的方法
dataType arrayRefVar[];  // 效果相同，但不是首选方法
```

#### 9.2 创建数组

```java
arrayRefVar = new dataType[arraySize];
```

上面的语法语句做了两件事：

- 一、使用 dataType[arraySize] 创建了一个数组。
- 二、把新创建的数组的引用赋值给变量 arrayRefVar。

```java
dataType[] arrayRefVar = {value0, value1, ..., valuek};
```

#### 9.3 处理数组

Java中允许有长度为0的数组(长度为0的数组与null并不相同).
创建一个数组时所有的元素都会初始化.

#### 9.4 for-each循环

用来依次处理数组或者其他元素集合中的每个元素,而不必考虑指定下标值.

for-each循环语句的循环变量将会遍历数组中的每个元素,而不是下标值.

##### 9.5 多维数组

```
type[][] typeName = new type[typeLength1][typeLength2];
```

```
type[][] typeName ={
					{....}
					{....}
					{....}
					}
```

