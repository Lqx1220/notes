## Spring优点

1. 轻量
2. 针对接口编程，解耦合:提供了IOC控制反转由容器管理对象,对象的依赖关系
   - 控制:创建对象,对象的属性赋值,对象之间的关系管理
   - 反转:把原本的开发人员管理,创建对象的权限转移给代码之外的容器实现,由容器代替开发人员管理对象,创建对象
   - 正转:由开发人员在代码中,使用new构造方法创建对象
   - 容器:是一个服务器软件,一个框架
3. AOP编程的支持:方便进行面向切面的编程

## 控制反转IOC

#### 使用Spring的IOC创建对象步骤

1. 创建maven项目

2. 加入maven的依赖

3. 创建类(接口和它的实现类)

4. 创建Spring需要使用的配置文件(在文件中声明类的信息,这些类由spring创建和管理)

   - 在src/main/resources/目录下创建xml文件(文件名随意,建议为applicationContext.xml)

   - ```xml
     <bean id="XXX" class="org.example.prot.impl.oneServletImpl" />
     ```

     - `<bean />`:用于定义一个实例对象,一个实例对应一个bean元素
     - id:该属性是Bean实例的唯一标识,程序通过id属性访问Bean,Bean和Bean间的依赖关系也是通过id属性关联的
     - class:指定该Bean所属的类(只能是类,不能是接口.是全限定类名)

5. 使用Spring容器创建对象

   1. 指定Spring配置文件的名称
   2. 创建表示Spring容器的对象
   3. 使用Spring创建好的对象

   ![image-20210919192857060](https://i.loli.net/2021/09/19/YNtDSRp3XFBCuAl.png)

#### Spring默认创建对象的时间

在创建Spring的容器时,会创建配置文件中的所有对象

#### 基于XML的DI

###### set注入

Spring调用类的set方法

- **简单类型**

Spring配置文件中加入参数

```xml
<bean  id="student" class="org.example.Student">
        <property name="name" value="嬴政"/>
        <property name="age" value="21"/>
    </bean>
```

其中Student类中必须要有setXXX方法(XXX为property中name首字母大写的字符串)

一个property只能给一个属性赋值 

- **引用类型**

`<bean id="xxx" class="yyy">`
		`<property name="属性名称" ref="bean的id(对象的名称)"/>`
		`</bean>`

1. ```xml
   <bean id="myschool" class="org.example.School">
   	<property name="name" value="秦"/>
       <property name="address" value="华夏"/>
   </bean>
   ```

   

2. ```xml
   <bean  id="student" class="org.example.Student">
           <property name="name" value="嬴政"/>
           <property name="age" value="21"/>
       	<property name="school" ref ="myschool"/>
   </bean>
   ```

   

###### 构造注入

spring调用类有参数构造方法，在创建对象的同时，在构造方法中给属性赋值。

构造注入使用 `<constructor-arg>` 标签

 `<constructor-arg>` 标签：一个`<constructor-arg>`表示构造方法一个参数。
          `<constructor-arg>` 标签属性：

- name:表示构造方法的形参名
- index:表示构造方法的参数的位置，参数从左往右位置是 0 ， 1 ，2的顺序
- value：构造方法的形参类型是简单类型的，使用value
- ref：构造方法的形参类型是引用类型的，使用ref

```xml
   <!--使用name属性实现构造注入-->
    <bean id="myStudent" class="com.bjpowernode.ba03.Student" >
        <constructor-arg name="myage" value="20" />
        <constructor-arg name="mySchool" ref="myXueXiao" />
        <constructor-arg name="myname" value="周良"/>
    </bean>

    <!--使用index属性-->
    <bean id="myStudent2" class="com.bjpowernode.ba03.Student">
        <constructor-arg index="1" value="22" />
        <constructor-arg index="0" value="李四" />
        <constructor-arg index="2" ref="myXueXiao" />
    </bean>

    <!--省略index-->
    <bean id="myStudent3" class="com.bjpowernode.ba03.Student">
        <constructor-arg  value="张强强" />
        <constructor-arg  value="22" />
        <constructor-arg  ref="myXueXiao" />
    </bean>
    <!--声明School对象-->
    <bean id="myXueXiao" class="com.bjpowernode.ba03.School">
        <property name="name" value="清华大学"/>
        <property name="address" value="北京的海淀区" />
    </bean>
```

###### 引用类型的自动注入

- byName自动注入

  当配置文件中被调用者 bean 的 id 值与代码中调用者 bean 类的属性名相同时，可使用byName 方式，让容器自动将被调用者 bean 注入给调用者bean。容器是通过调用者的 bean类的属性名与配置文件的被调用者 bean 的id 进行比较而实现自动注入的。

![image-20210823110846561](https://i.loli.net/2021/08/23/ZaD1A52QRh9bLrp.png)



```xml
<bean id="student" class="yk.Student" autowire="byName">
    <property name="name" value="yk"/>
    <property name="age" value="21"/>

</bean>
    <bean id="school" class="yk.School">
        <property name="address" value="青岛"/>
        <property name="sName" value="青岛大学"/>
    </bean>
</beans>
```

- byType自动注入

  使用 byType 方式自动注入，要求：配置文件中被调用者 bean 的 class 属性指定的类，要与代码中调用者 bean 类的某引用类型属性类型同源。即要么相同，要么有 is-a 关系（子类，或是实现类）。但这样的同源的被调用 bean 只能有一个。多于一个，容器就不知该匹配哪一个了。

  

  ![image-20210823111729562](https://i.loli.net/2021/08/23/dcUErVRyp95oqHZ.png)

  

  ```xml
  <bean id="student" class="yk.Student" autowire="byType">
      <property name="name" value="yk"/>
      <property name="age" value="21"/>
  
  </bean>
      <bean id="mySchool" class="yk.School">
          <property name="address" value="青岛"/>
          <property name="sName" value="青岛大学"/>
      </bean>
  </beans>
  ```

###### 指定多个Spring配置文件

```xml
<import resource="school.xml"/>
<import resource="student.xml"/>
```

<img src="https://i.loli.net/2021/08/23/BEb13vIDnTXQFLP.png" alt="image-20210823113314338"  />

#### 基于注解的DI

###### 步骤

1. 加入maven的依赖spring-context<!--间接加入spring-aop依赖-->

2. 在类中加入Spring的注解

3. 在Spring的配置文件中加入一个组件扫描器的标签,说明注解在你项目中的位置

   ```xml
   <context:component-scan base-package="xxx"/>
   //xxx:指定要扫描的包名,可以扫描其中的所有包中的所有类
   //有多个包要扫描可以用逗号（，）分号（；）还可以使用空格，不建议使用空格。也可以多次指定<context:component-scan  />
   ```

   

4. 使用注解创建对象,创建对象容器ApplicationContext

###### @Component注解

- 作用:创建对象的， 等同于<bean>的功能
- 属性：value 就是对象的名称，也就是bean的id值，
   *          value的值是唯一的，创建的对象在整个spring容器中就一个
 *          位置：在类的上面

```xml
@Component(value = "myStudent")
<!-- 可以省略value  @Component("myStudent")-->
<!--可以不指定对象名称由spring提供默认名称: 类名的首字母小写 @Component-->

<!-- <bean id="myStudent" class="com.bjpowernode.ba01.Student" /> -->
```

1. @Repository（用在持久层类的上面） : 放在dao的实现类上面，表示创建dao对象，dao对象是能访问数据库的。
2. @Service(用在业务层类的上面)：放在service的实现类上面，创建service对象，service对象是做业务处理，可以有事务等功能的。
3. .@Controller(用在控制器的上面)：放在控制器（处理器）类的上面，创建控制器对象的，控制器对象，能够接受用户提交的参数，显示请求的处理结果。

 以上三个注解的使用语法和@Component一样的。 都能创建对象，但是这三个注解还有额外的功能。

 *  @Repository，@Service，@Controller是给项目的对象分层的

###### @Value

- 作用： 简单类型的属性赋值
- 属性： value 是String类型的，表示简单类型的属性值
- 位置：
  1. 在属性定义的上面，无需set方法，推荐使用。
  2. 在set方法的上面

```Java
@Component(value = "myStudent")
public class Student {
    @Value("eminem")
    private String name;
    @Value("55")
    private Integer age;
    }
```

###### @Autowired

- 作用：spring框架提供的注解，实现引用类型的赋值。
- 属性：使用的是自动注入原理 ，支持byName, byType 。
  - 默认使用的是byType自动注入。
  - 在属性上面加入@Qualifier(value="bean的id") ：表示使用指定名称的bean完成赋值<!-- byName自动注入-->
  - required ，是一个boolean类型的，默认true
    - required=true：表示引用类型赋值失败，程序报错，并终止执行。
    -  required=false：引用类型如果赋值失败， 程序正常执行，引用类型是null
- 位置：
  1. 在属性定义的上面，无需set方法， 推荐使用
  2. 在set方法的上面

```Java
@Component(value = "myStudent")
public class Student {
    @Value("eminem")
    private String name;
    @Value("55")
    private Integer age;
    @Autowired
    //@Qualifier(value = "myschool") byName自动注入
    private School school
}
```

```Java
@Component("myschool")
public class School {
    @Value("青岛大学")
    private String sName;
    @Value("青岛")
    private String address;
    //xxx
}
```

###### @Resource

- 作用：来自jdk中的注解，spring框架提供了对这个注解的功能支持，可以使用它给引用类型赋值使用的也是自动注入原理，支持byName， byType .默认是byName

  <!--默认是byName： 先使用byName自动注入，如果byName赋值失败，再使用byType-->

- 属性：name ，name的值是bean的id（名称）<!--使用后只能使用byName自动注入-->

- 位置：
  1. 在属性定义的上面，无需set方法，推荐使用。
  2. 在set方法的上面

## AOP面向切面编程

#### AOP定义：

基于动态代理，将动态代理规范化。可以使用jdk，cglib两种代理方式。

#### 切面

- 定义：给你的目标类增加的功能，就是切面。
- 特点：一般都是非业务方法，独立使用的。

#### 面向切面编程

1. 需要在分析项目功能时，找出切面。
2. 合理的安排切面的执行时间（在目标方法前， 还是目标方法后）
3. 合理的安全切面执行的位置，在哪个类，哪个方法增加增强功能

###### 术语

- Aspect:切面，表示增强的功能，完成某个一个功能。非业务功能，
- JoinPoint:连接点  ，连接业务方法和切面的位置。 就某类中的业务方法
- Pointcut : 切入点 ，指多个连接点方法的集合。多个方法
- 目标对象： 给哪个类的方法增加功能， 这个类就是目标对象
- Advice:通知，通知表示切面功能执行的时间。

###### 切面的三个关键要素

1. 切面的功能代码，切面干什么
2. 切面的执行位置，使用Pointcut表示切面执行的位置
3. 切面的执行时间，使用Advice表示时间，在目标方法之前，还是目标方法之后。

#### aop的实现

- spring：spring在内部实现了aop规范，能做aop的工作。<!--比较笨重，很少使用-->
- aspectJ: 一个开源的专门做aop的框架。spring框架中集成了aspectj框架，通过spring就能使用aspectj的功能。
  - 使用xml的配置文件 ： 配置全局事务
  - 使用注解，我们在项目中要做aop功能，一般都使用注解， aspectj有5个注解。

#### AspectJ

###### AspectJ 的切入点表达式

```Java
execution(modifiers-pattern? ret-type-pattern

declaring-type-pattern?name-pattern(param-pattern)

throws-pattern?)
```

- modifiers-pattern 访问权限类型 
- ret-type-pattern 返回值类型 
- declaring-type-pattern  包名类名 
- name-pattern(param-pattern) 方法名(参数类型和参数个数)
- throws-pattern  抛出异常类型 
- ？表示可选的部分 

![image-20210823164102166](https://i.loli.net/2021/08/23/vXlwWVZorFINs2H.png)

###### AspectJ实现aop的基本步骤

1. 加入依赖
   1. Spring依赖
   2. aspectj依赖
   3. junit单元测试
   
2. 创建目标类：接口和它的实现类

3. 创建切面类：普通类
   1. 在类的上面加入@Aspect
   2. 在类中定义方法，方法就是切面要执行的功能代码在方法的上面加入aspectj中的通知注解，有需要指定切入点表达式execution（）
   
4. 创建spring的配置文件：声明对象，把对象交给容器统一管理声明对象你可以使用注解或者xml文件<bean>
   1. 声明目标对象
   2. 声明切面对象
   3. 声明aspectj框架中自动代理生成器标签
      - 自动代理生成器：用来完成代理对象的自动创建功能
   
   ![image-20210919191237578](https://i.loli.net/2021/09/19/Ami3l7S89jwCypz.png)
   
5. 创建测试类，从spring容器中获取目标对象（实际就是代理对象）

   ![image-20210919191317514](https://i.loli.net/2021/09/19/2pdu6lTvIe8XKMY.png)

###### @Aspect

- 作用：表示当前类是切面类。
- 切面类：是用来给业务方法增加功能的类，在这个类中有切面的功能代码
- 位置：在类定义的上面

###### @Before

- 定义：前置通知注解


- 属性：value ，是切入点表达式，表示切面的功能执行的位置。


- 位置：在方法的上面


- 特点：
  1. 在目标方法之前先执行的
  2. 不会改变目标方法的执行结果
  3. 不会影响目标方法的执行。

- 切面方法
  1. 公共方法 public
  2. 方法没有返回值
  3. 方法名称自定义
  4. 前置通知注解:方法可以有参数，也可以没有参数。如果有参数，参数不是自定义的，有几个参数类型可以使用。

###### JoinPoint

- 定义 指定通知方法中的参数
- 用法:切面功能需要用到方法的信息时加入JoinPoint
- 作用:可以在通知方法中获取方法执行时的信息<!--例如方法名称,方法的实参-->
- 位置:JoinPoint参数的值是由框架赋予,必须是第一个位置的参数

```Java
@Aspect
public class MyAspect {
    @Before(value = "execution(public void doSome(String ,Integer))")
    public void as(JoinPoint jp){
        final Object[] args = jp.getArgs();
        for (int i = 0; i < args.length; i++) {
            System.out.println(args[i]);
        }
        System.out.println("切面方法"+ LocalTime.now());
    }
}
```

###### @AfterReturning

- 属性：
  1. value 切入点表达式
  2. returning 自定义的变量，表示目标方法的返回值的。自定义变量名必须和通知方法的形参名一样。
- 位置：在方法定义的上面
-  特点:
  1. 在目标方法之后执行的。
  2. 能够获取到目标方法的返回值，可以根据这个返回值做不同的处理功能
  3. 可以修改这个返回值
- 切面方法
  1. 公共方法 public
  2. 方法没有返回值
  3. 方法名称自定义
  4. 后置通知注解:方法有参数的,推荐是Object ，参数名自定义

```Java
@Aspect
public class MyAspect {
    @AfterReturning(value = "execution(Student  doSome(String,Integer)) ",returning = "obj")
    public void myAfter(Object obj){
        System.out.println("after:====>"+obj);
        Student student = (Student) obj;
        student.setAge(1111111);
    }
}
```

###### @Around

- 定义:环绕通知
- 属性:value 切入点表达式
- 位置:在方法的定义什么
- 特点
  1. 它是功能最强的通知
  2. 在目标方法的前和后都能增强功能
  3. 控制目标方法是否被调用执行
  4. 修改原来的目标方法的执行结果。 影响最后的调用结果

- 切面方法
  1. 公共方法 public
  2. 必须有一个返回值，推荐使用Object
  3. 方法名称自定义
  4. 方法有参数，固定的参数 ProceedingJoinPoint

 环绕通知，等同于jdk动态代理的，InvocationHandler接口

参数：  ProceedingJoinPoint 就等同于 Method  作用：执行目标方法的

返回值： 就是目标方法的执行结果，可以被修改。



接口 ProceedingJoinPoint 其有一个proceed()方法，用于执行目标方法。若目标方法有返回值，则该方法的返回值就是目标方法的返回值。最后，环绕增强方法将其返回值返回。该增强方法实际是拦截了目标方法的执行。

```java
  @Around(value = "execution(* *..SomeServiceImpl.doFirst(..))")
    public Object myAround(ProceedingJoinPoint pjp) throws Throwable {

        String name = "";
        //获取第一个参数值
        Object args [] = pjp.getArgs();
        if( args!= null && args.length > 1){
              Object arg=  args[0];
              name =(String)arg;
        }

        //实现环绕通知
        Object result = null;
        System.out.println("环绕通知：在目标方法之前，输出时间："+ new Date());
        //1.目标方法调用
        if( "zhangsan".equals(name)){
            //符合条件，调用目标方法
            result = pjp.proceed(); //method.invoke(); Object result = doFirst();

        }

        System.out.println("环绕通知：在目标方法之后，提交事务");
        //2.在目标方法的前或者后加入功能

        //修改目标方法的执行结果， 影响方法最后的调用结果
        if( result != null){
              result = "Hello AspectJ AOP";
        }

        //返回目标方法的执行结果
        return result;
    }

```

![image-20210919191601854](https://i.loli.net/2021/09/19/ohtKgfmea4Usln7.png)

###### @AfterThrowing

- 异常通知

-  属性：
  1. value 切入点表达式
  2. throwinng 自定义的变量，表示目标方法抛出的异常对象。 变量名必须和方法的参数名一样
  
- 特点：
  1. 在目标方法抛出异常时执行的
  2. 可以做异常的监控程序， 监控目标方法执行时是不是有异常。
  
- 切面方法
  1. 公共方法 public
  2. 方法没有返回值
  3. 方法名称自定义
  4. 方法有一个Exception参数
  
  ```java
   @AfterThrowing(value = "execution(* *..SomeServiceImpl.doSecond(..))",
              throwing = "ex")
      public void myAfterThrowing(Exception ex) {
          System.out.println("异常通知：方法发生异常时，执行："+ex.getMessage());
          //发送邮件，短信，通知开发人员
      }
  ```
  
  

###### @After

- 最终通知

- 属性： value 切入点表达式

- 位置： 在方法的上面

- 特点：
  1. 总是会执行
  2. 在目标方法之后执行的
  
- 切面方法
  1. 公共方法 public
  2. 方法没有返回值
  3. 方法名称自定义
  4. 方法没有参数，  如果有就是JoinPoint
  
  ```java
    @After(value = "execution(* *..SomeServiceImpl.doThird(..))")
      public  void  myAfter(){
          System.out.println("执行最终通知，总是会被执行的代码");
          //一般做资源清除工作的。
       }
  ```

###### @Pointcut

- 定义和管理切入点,项目中有多个切入点表达式是重复的，可以复用的。
- 属性：value 切入点表达式
-  位置：在自定义的方法上面
- 特点
  -  当使用@Pointcut定义在一个方法的上面 ，此时这个方法的名称就是切入点表达式的别名。
  - 其它的通知中，value属性就可以使用这个方法名称，代替切入点表达式了
- 切面方法
  1. 一般是private
  2. 无返回值
  3. 名称自定义
  4. 没有参数

```Java
@Aspect
public class MyAspect {
    @After(value = "pc()")
    public void mm(){
        System.out.println("---->>>>");
    }
    @Before(value = "pc()")
    public void mm1(){
        System.out.println("<<<<<<<<<<<<");
    }
    @Pointcut(value = "execution(String doSome(..))")
    private void pc(){}
}
```

###### cglib

- 如果目标类中没有接口,则spring框架会自动应用cglib<!--因为没有接口所以无法使用动态代理-->
- 如果目标类有接口,且目标类符合cglib的应用方式.在applicationContext中配置`<aop:aspectj-autoproxy proxy-target-class="true"/>`则不使用默认的动态代理而使用cglib

## spring整合mybatis

#### 步骤

1. 新建maven项目

2. 加入maven的依赖
   1. spring依赖
   2. mybatis依赖
   3. mysql驱动
   4. spring事务依赖
   5. mybatis和spring集成依赖<!--用来在spring项目中创建mybatis的SqlSessissonFactory,dao对象-->
   
3. 创建实体类

4. 创建dao接口和mapper文件

5. 创建mybatis主配置文件

6. 创建Service接口和实现类,属性是dao

   <img src="https://i.loli.net/2021/09/19/We6pl4AJrtSiIFk.png" alt="image-20210919211030545" style="zoom: 25%;" />

   <img src="https://i.loli.net/2021/10/12/1mPp86QtIMAgelH.png" alt="image-20210919211110948" style="zoom: 50%;" />

7. 创建spring的配置文件:声明mybatis 的对象交给spring创建
   1. 数据源DataSource
   2. SqlSessionFactory
   3. Dao对象
   4. 声明自定义的service

8. 创建测试类,获取Serivce对象,通过service调用dao完成数据库的访问

```xml
<!--
       把数据库的配置信息，写在一个独立的文件，编译修改数据库的配置内容
       spring知道jdbc.properties文件的位置
    -->
    <context:property-placeholder location="classpath:jdbc.properties" /> 
<!--声明数据源DataSource, 作用是连接数据库的-->
    <bean id="myDataSource" class="com.alibaba.druid.pool.DruidDataSource"
          init-method="init" destroy-method="close">
        <!--set注入给DruidDataSource提供连接数据库信息 -->
        <!--    使用属性配置文件中的数据，语法 ${key} -->
        <property name="url" value="${jdbc.url}" /><!--setUrl()-->
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.passwd}" />
        <property name="maxActive" value="${jdbc.max}" />
    </bean>


 <!--声明的是mybatis中提供的SqlSessionFactoryBean类，这个类内部创建SqlSessionFactory的
        SqlSessionFactory  sqlSessionFactory = new ..
    -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--set注入，把数据库连接池付给了dataSource属性-->
        <property name="dataSource" ref="myDataSource" />
        <!--mybatis主配置文件的位置
           configLocation属性是Resource类型，读取配置文件
           它的赋值，使用value，指定文件的路径，使用classpath:表示文件的位置
		在spring配置文件中指定其他配置文件需要用classpath
        -->
        <property name="configLocation" value="classpath:mybatis.xml" />
    </bean>



    <!--创建dao对象，使用SqlSession的getMapper（StudentDao.class）
        MapperScannerConfigurer:在内部调用getMapper()生成每个dao接口的代理对象。

    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--指定SqlSessionFactory对象的id-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
        <!--指定包名， 包名是dao接口所在的包名。
            MapperScannerConfigurer会扫描这个包中的所有接口，把每个接口都执行
            一次getMapper()方法，得到每个接口的dao对象。
            创建好的dao对象放入到spring的容器中的。 dao对象的默认名称是 接口名首字母小写
        -->
        <property name="basePackage" value="com.bjpowernode.dao"/>
    </bean>



    <!--声明service-->
    <bean id="studentService" class="com.bjpowernode.service.impl.StudentServiceImpl">
        <property name="studentDao" ref="studentDao" />
        <!--ref中的studentDao是 创建好的dao对象的默认名称是 接口名首字母小写-->
    </bean>

```

#### spring事务

###### spring控制事务的位置

service类的业务方法上,因为业务方法会调用多个dao方法,执行多个SQL语句

###### 事务管理器

- 接口:platformTransactionManager 定义了事务重要方法commit , rollback

-  实现类：spring把每一种数据库访问技术对应的事务处理类都创建好了。
  -  mybatis访问数据库---spring创建好的是DataSourceTransactionManager
  -  hibernate访问数据库----spring创建的是HibernateTransactionManager

- Spring的配置文件中的声明

  ```xml
  <!--mybatis访问数据库的配置文件-->
  <bean id=“xxx" class="...DataSourceTransactionManager"> 
  ```

###### 事务的隔离

事务定义接口TransactionDefinition 中定义了事务描述相关的三类常量：事务隔离级别、事务传播行为、事务默认超时时限，及对它们的操作。 

- DEFAULT：采用 DB 默认的事务隔离级别。MySql 的默认为 REPEATABLE_READ； Oracle默认为 READ_COMMITTED。
- 这些常量均是以 ISOLATION_开头。即形如ISOLATION_XXX。
  - READ_UNCOMMITTED：读未提交。未解决任何并发问题。
  - READ_COMMITTED：读已提交。解决脏读，存在不可重复读与幻读。
  - REPEATABLE_READ：可重复读。解决脏读、不可重复读，存在幻读
  - SERIALIZABLE：串行化。不存在并发问题。

###### 事务的超时时间

表示一个方法最长的执行时间，如果方法执行时超过了时间，事务就回滚。单位是秒， 整数值， 默认是 -1. 

###### 事务的传播行为 

- PROPAGATION_REQUIRED
  - 指定的方法必须在事务内执行。若当前存在事务，就加入到当前事务中；若当前没有事务，则创建一个新事务。这种传播行为是最常见的选择

- PROPAGATION_ SUPPORTS
  - 指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。

- PROPAGATION_REQUIRES_NEW
  - 总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。

- 提交事务,回滚事务的时机(默认方式)
  1. 当你的业务方法，执行成功，没有异常抛出，当方法执行完毕，spring在方法执行后提交事务。事务管理器commit
  2. 当你的业务方法抛出运行时异常或ERROR， spring执行回滚，调用事务管理器的rollback
  3. 当你的业务方法抛出非运行时异常， 主要是受查异常时，提交事务

#### 实现注解的事务步骤

1. 声明事务管理器

   ```xml
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <!--连接的数据库， 指定数据源-->
           <property name="dataSource" ref="myDataSource"/>
       </bean>
   ```

2. 开启注解驱动

   ```xml
   <!--开启事务注解驱动，告诉spring使用注解管理事务，创建代理对象.transaction-manager:事务管理器对象的id-->
   <tx:annotation-driven transaction-manager="transactionManager"/>
   ```

   

3. 业务层public方法加入事务的注解<!--在public方法上加入注解或者类名上加入注解-->

![image-20210826173858427](https://i.loli.net/2021/08/26/lXDv9yfkIK2gaH7.png)

###### @Transactional

- 属性

propagation：用于设置事务传播属性。该属性类型为 Propagation 枚举，默认值为Propagation.REQUIRED。 

isolation：用于设置事务的隔离级别。该属性类型为 Isolation 枚举，默认值为Isolation.DEFAULT。

readOnly：用于设置该方法对数据库的操作是否是只读的。该属性为 boolean，默认值为 false。

timeout：用于设置本操作与数据库连接的超时时限。单位为秒，类型为 int，默认值为1，即没有时限。

rollbackFor：指定需要回滚的异常类。类型为 Class[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

rollbackForClassName：指定需要回滚的异常类类名。类型为String[]，默认值为空数组。当然，若只有一个异常类时，可以不使用组。

noRollbackFor：指定不需要回滚的异常类。类型为Class[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

noRollbackForClassName：指定不需要回滚的异常类类名。类型为 String[]，默认值为空数组。当然，若只有一个异常类时，可以不使用数组。

- Spring 会忽略掉所有非public 方法上的@Transaction 注解。 在类上加入@Transaction 注解说明所有的public都具有事务
- rollbackFor:表示发生指定的异常一定回滚.
   处理逻辑是：
  1. spring框架会首先检查方法抛出的异常是不是在rollbackFor的属性值中,如果异常在rollbackFor列表中，不管是什么类型的异常，一定回滚。
  2. 如果你的抛出的异常不在rollbackFor列表中，spring会判断异常是不是RuntimeException,如果是一定回滚。

#### Aspectj的AOP配置管理事务步骤

1. 在maven中加入aspectj依赖

2. 在容器中添加事务管理器

   ```xml
   <!--mysql的访问数据库的配置文件--> 
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <!--连接的数据库， 指定数据源-->
           <property name="dataSource" ref="myDataSource"/>
       </bean>
   ```

3. 配置事务通知

   ```xml
   <!--2.声明业务方法它的事务属性（隔离级别，传播行为，超时时间）
             id:自定义名称，表示 <tx:advice> 和 </tx:advice>之间的配置内容的
             transaction-manager:事务管理器对象的id
       -->
       <tx:advice id="myAdvice" transaction-manager="transactionManager">
           <!--tx:attributes：配置事务属性-->
           <tx:attributes>
               <!--tx:method：给具体的方法配置事务属性，method可以有多个，分别给不同的方法设置事务属性
                   name:方法名称，1）完整的方法名称，不带有包和类。
                                 2）方法可以使用通配符,* 表示任意字符
                   propagation：传播行为，枚举值
                   isolation：隔离级别
                   rollback-for：你指定的异常类名，全限定类名。 发生异常一定回滚
               -->
               <tx:method name="buy" propagation="REQUIRED" isolation="DEFAULT"
                          rollback-for="java.lang.NullPointerException,com.bjpowernode.excep.NotEnoughException"/>
   
               <!--使用通配符，指定很多的方法-->
               <tx:method name="add*" propagation="REQUIRES_NEW" />
               <!--指定修改方法-->
               <tx:method name="modify*" />
               <!--删除方法-->
               <tx:method name="remove*" />
               <!--查询方法，query，search，find-->
               <tx:method name="*" propagation="SUPPORTS" read-only="true" />
           </tx:attributes>
       </tx:advice>
   ```

4. 配置增强器

```xml
<!--配置aop-->
    <aop:config>
        <!--配置切入点表达式：指定哪些包中类，要使用事务
            id:切入点表达式的名称，唯一值
            expression：切入点表达式，指定哪些类要使用事务，aspectj会创建代理对象

            com.bjpowernode.service
            com.crm.service
            com.service
        -->
        <aop:pointcut id="servicePt" expression="execution(* *..service..*.*(..))"/>

        <!--配置增强器：关联adivce和pointcut
           advice-ref:通知，上面tx:advice哪里的配置
           pointcut-ref：切入点表达式的id
        -->
        <aop:advisor advice-ref="myAdvice" pointcut-ref="servicePt" />
    </aop:config>
```

## web项目中使用spring

#### 步骤

1. 创建maven，web项目

2. 加入maven的依赖
   1. spring依赖
   2. mybatis依赖
   3. mysql驱动
   4. spring事务依赖
   5. mybatis和spring集成依赖
   6. servlet (jsp)依赖
   
3. 创建实体类

4. 创建dao接口和mapper文件

5. 创建mybatis主配置文件

6. 创建Service接口和实现类,属性是dao

7. 创建spring的配置文件:声明mybatis 的对象交给spring创建
   1. 数据源DataSource
   2. SqlSessionFactory
   3. Dao对象
   4. 声明自定义的service
   
8. 创建表单发起请求

9. 创建Servlet接收请求参数调用Servlet,调用dao

   ```java
   public class RegisterServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
   
           String strId = request.getParameter("id");
           String strName = request.getParameter("name");
           String strEmail = request.getParameter("email");
           String strAge = request.getParameter("age");
   
           //创建spring的容器对象
           //String config= "spring.xml";
           //ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
           WebApplicationContext ctx = null;
           //获取ServletContext中的容器对象，创建好的容器对象，拿来就用
           /*String key =  WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE;
           Object attr  = getServletContext().getAttribute(key);
           if( attr != null){
               ctx = (WebApplicationContext)attr;
           }*/
   
           //使用框架中的方法，获取容器对象
           ServletContext sc = getServletContext();
           ctx = WebApplicationContextUtils.getRequiredWebApplicationContext(sc);
           System.out.println("容器对象的信息========"+ctx);
   
           //获取service
           StudentService service  = (StudentService) ctx.getBean("studentService");
           Student student  = new Student();
           student.setId(Integer.parseInt(strId));
           student.setName(strName);
           student.setEmail(strEmail);
           student.setAge(Integer.valueOf(strAge));
           service.addStudent(student);
   
           //给一个页面
           request.getRequestDispatcher("/result.jsp").forward(request,response);
   
       }
   ```

   

10. 创建结果显式页面

#### spring中的监听器

###### 作用

web项目中容器对象只需要创建一次，  把容器对象放入到全局作用域ServletContext中。

1. 创建容器对象,执行 `ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");`
2. 把容器对象放入到ServletContext， ServletContext.setAttribute(key,ctx)

###### 使用

监听器被创建对象后，会读取/WEB-INF/spring.xml
        为什么要读取文件：因为在监听器中要创建ApplicationContext对象，需要加载配置文件。
        /WEB-INF/applicationContext.xml就是监听器默认读取的spring配置文件路径

  可以修改默认的文件位置，使用context-param重新指定文件的位置

```xml
	<context-param>
        <!-- contextConfigLocation:表示配置文件的路径  -->
        <param-name>contextConfigLocation</param-name>
        <!--自定义配置文件的路径-->
        <param-value>classpath:spring.xml</param-value>
    </context-param>
	<!--注册监听器ContextLoaderListener-->
	<listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```

使用框架中的方法,获取容器对象

```Java
		//String config= "spring.xml";
        //ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
 		ServletContext sc = getServletContext();
        ctx = WebApplicationContextUtils.getRequiredWebApplicationContext(sc);
```

