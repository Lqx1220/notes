## Spring基础

#### SpringBoot多环境配置

所有配置以`application-`开头:application-dev.properties

主配置文件:

```properties
#yml,yaml同理
spring.profiles.active=dev
```

#### SpringBoot自定义配置

###### 

```properties
school=aa
my.name=yk
my.age=21
```

方法一

```java
@RestController
public class MyController {
    @Value("${my.name}")
    private String name;
    @Value("${my.age}")
    private String age;
    @Value("${school}")
    private String school;

    @RequestMapping("/some")
    public String show() {
        return name+"---"+age+"---"+school;
    }
}
```

###### 方法二

```Java
package org.yk.domain;
@Component
//将整个文件映射成一个对象，用于自定义配置项比较多的情况,要求前缀是统一的
@ConfigurationProperties(prefix = "my")
public class Student {
    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

```Java
package org.yk.controller;
@RestController
public class MyController {
  @Autowired
  private Student student;
    @RequestMapping("/some")
    public String show() {
        return student.getName()+"------"+ student.getAge();
    }
}
```

#### @RestController

相当于控制层类上加@Controller + 方法上加@ResponseBody

#### 各种请求注解

```java
//该方法请求方式支持:GET和POST请求
    @RequestMapping(value = "/queryStudentById",method = {RequestMethod.GET,RequestMethod.POST})
    public Object queryStudentById(Integer id) {
        Student student = new Student();
        student.setId(id);
        return student;
    }


//    @RequestMapping(value = "/queryStudentById2",method = RequestMethod.GET)
    @GetMapping(value = "/queryStudentById2") //相当于上一句话,只接收GET请求,如果请求方式不对会报405错误
    //该注解通过在查询数据的时候使用 -> 查询
    public Object queryStudentById2() {
        return "Ony GET Method";
    }

//    @RequestMapping(value = "/insert",method = RequestMethod.POST)
    @PostMapping(value = "/insert") //相当于一句话
    //该注解通常在新增数据的时候使用 -> 新增
    public Object insert() {
        return "Insert success";
    }

//    @RequestMapping(value = "/delete",method = RequestMethod.DELETE)
    @DeleteMapping(value = "/delete")//相当于上一句话
    //该注解通常在删除数据的时候使用 -> 删除
    public Object delete() {
        return "delete Student";
    }


//    @RequestMapping(value = "/update",method = RequestMethod.PUT)
    @PutMapping(value = "/update") //相当于上一句话
    //该注解通常在修改数据的时候使用 -> 更新
    public Object update() {
        return "update student info1";
    }
```



## SpringBoot集成MyBatis

#### 步骤

1. 在pom.xml中添加jar依赖

   ```xml
           <!--mysql驱动依赖-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
           </dependency>
           <!--mybatis整合springboot起步依赖-->
           <dependency>
               <groupId>org.mybatis.spring.boot</groupId>
               <artifactId>mybatis-spring-boot-starter</artifactId>
               <version>2.2.0</version>
           </dependency>
     
   ```

2. 在 Springboot 的核心配置文件 application.properties 中配置数据源

   ```properties
   #配置内嵌服务器端口号
   server.port=8080
   #配置项目上下文根
   server.servlet.context-path=/
   #配置数据库连接信息
   #必须要配mysql驱动
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   spring.datasource.url=jdbc.url=jdbc:mysql://localhost:3306/springdb?useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
   spring.datasource.username=root
   spring.datasource.password=959977
   ```

3. 创建实体类,编写dao层接口,编写dao接口的mapping映射文件

4. 编写controller类



#### 扫面Dao接口到Spring容器

###### 方法一:@Mapper

```Java
package org.yk.mapper;
@Mapper
public interface StudentMapper {
    Student selectByPrimaryKey(Integer id);
}
```

###### 方法二:@MapperScan

```java

package org.yk;
@SpringBootApplication
@MapperScan(basePackages = "org.yk.mapper")
public class CSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(CSpringbootApplication.class, args);
    }

}
```

#### 将MyBatis的xml映射文件编译到targer的class目录下

###### 方法一

- 默认情况下，Mybatis 的xml 映射文件不会编译到 target 的 class 目录下，所以我们需要在 pom.xml 文件中配置 resource

```xml
<resources>
    <resource> 
     <directory>src/main/java</directory> 
      <includes> 
      	<include>**/*.xml</include> 
       </includes> 
     </resource> 
</resources> 
```

###### 方法二

1. 将Mybatis 的xml 映射文件放到resource目录下
2. 在application.properties 配置文件中指定映射文件的位置，这个配置只有接口和映射文件不在同一个包的情况下，才需要指定

```properties
	# 指定Mybatis映射文件的路径 (这个文件在resource的mapper文件夹下)
	mybatis.mapper-locations=classpath:mapper/*.xml
```

#### 开启事务

- 在 StudentServiceImpl 接口实现类中对需要加事务的方法上加入**@Transactional 注解** 

- 在Application类上加@EnableTransactionManagement

@EnableTransactionManagement 可选，但是业务方法上必须添加@Transactional 事务才生效 

#### RESTFul(了解)

```Java
//    @RequestMapping(value = "/student/detail/{id}/{age}")
    @GetMapping(value = "/student/detail/{id}/{age}")
    public Object student1(@PathVariable("id") Integer id,
                           @PathVariable("age") Integer age) {
        Map<String,Object> retMap = new HashMap<>();

        retMap.put("id",id);
        retMap.put("age",age);
        return retMap;
    }

//    @RequestMapping(value = "/student/detail/{id}/{status}")
    @DeleteMapping(value = "/student/detail/{id}/{status}")
    public Object student2(@PathVariable("id") Integer id,
                           @PathVariable("status") Integer status) {
        Map<String,Object> retMap = new HashMap<>();

        retMap.put("id",id);
        retMap.put("status",status);
        return retMap;
    }

    //以上代码student1和student2会出现请求路径冲突问题
    //通常在RESTful风格中方法的请求方式会按增删改查的请求方式来区分
    //修改请求路径
    //RESUful请求风格要求路径中使用的单词都是名称,最好不要出现动词

    @DeleteMapping(value = "/student/{id}/detail/{city}")
    public Object student3(@PathVariable("id") Integer id,
                           @PathVariable("city") Integer city) {
        Map<String,Object> retMap = new HashMap<>();

        retMap.put("id",id);
        retMap.put("city",city);
        return retMap;
    }

    @PostMapping(value = "/student/{id}")
    public String addStudent(@PathVariable("id") Integer id) {
        return "add student ID:" + id;
    }

    @PutMapping(value = "/student/{id}")
    public String updateStudent(@PathVariable("id") Integer id) {
        return "update Student ID:" +id;
    }
```

## Spring创建非Web工程(了解)

```java
public interface StudentService {

    /**
     * SayHello
     * @return
     */
    String sayHello();
}
```



```Java
@Service
public class StudentServiceImpl implements StudentService {
    @Override
    public String sayHello() {
        return "Say Hello";
    }
}
```

#### 方法一

```java
@SpringBootApplication
public class Application {


    public static void main(String[] args) {
        /**
         * Springboot程序启动后,返回值是ConfigurableApplicationContext,它也是一个Spring容器
         * 它其实相当于原来Spring容器中启动容器ClasspathXmlApplicationContext
         */

        SpringApplication.run(Application.class, args);

        //获取Springboot容器
        ConfigurableApplicationContext applicationContext = SpringApplication.run(Application.class, args);

        //从spring容器中获取指定bean对象
        StudentService studentService = (StudentService) applicationContext.getBean("studentServiceImpl");

        //调用业务方法
        String sayHello = studentService.sayHello();

        System.out.println(sayHello);

    }

}
```

#### 方法二

```java
@SpringBootApplication  //开启spring配置
public class Application implements CommandLineRunner {

    @Autowired
    private StudentService studentService;

    public static void main(String[] args) {
        //SpringBoot启动程序,会初始化Spring容器
        SpringApplication.run(Application.class, args);
    }


    //重写CommandLineRunner类中的run方法
    @Override
    public void run(String... args) throws Exception {

        //调用业务方法
        String sayHello = studentService.sayHello("World");

        System.out.println(sayHello);
    }
}
```

#### 关闭SpringBoot图标

```Java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {

//        SpringApplication.run(Application.class,args);

        //获取入口SpringBoot类
        SpringApplication springApplication = new SpringApplication(Application.class);

        //设置它的属性
        springApplication.setBannerMode(Banner.Mode.OFF);

        springApplication.run(args);
    }
}
```

#### 修改SpringBoot图标

在 src/main/resources 放入 banner.txt 文件

## SpringBoot拦截器

#### 步骤

1. 自定义拦截类

2. 创建配置类

   ```java
   @Configuration  //定义此类为配置文件(即相当于之前的xml配置文件)
   public class InterceptorConfig implements WebMvcConfigurer {
   
       //mvc:interceptors
       @Override
       public void addInterceptors(InterceptorRegistry registry) {
   
           //要拦截user下的所有访问请求,必须用户登录后才可访问,
           // 但是这样拦截的路径中有一些是不需要用户登录也可访问的
           String[] addPathPatterns = {
               "/user/**"
           };
   
           //要排除的路径,排除的路径说明不需要用户登录也可访问
           String[] excludePathPatterns = {
               "/user/out", "/user/error","/user/login"
           };
   
           //mvc:interceptor bean class=""
           //UserInterceptor()自定义拦截类
           registry.addInterceptor(new UserInterceptor()).addPathPatterns(addPathPatterns).excludePathPatterns(excludePathPatterns);
       }
   }
   ```

## SpringBoot使用Servlet

#### 方法一

###### 步骤

1. 创建servlet类加上@WebServle注解

   ```Java
   @WebServlet(urlPatterns = "/myservlet")//指定路径
   public class MyServlet extends HttpServlet {
   
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          resp.getWriter().println("My SpringBoot Servlet-1");
          resp.getWriter().flush();
          resp.getWriter().close();
   
       }
   
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           doGet(req, resp);
       }
   }
   ```

2. 扫描@WebServle注解

   ```java
   @SpringBootApplication  //开启spring配置
   @ServletComponentScan(basePackages = "com.bjpowernode.springboot.servlet")
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   
   }
   ```

#### 方法二

###### 步骤

1. 创建servlet类

   ```Java
   public class MyServlet extends HttpServlet {
   
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
   
           resp.getWriter().println("My SpringBoot Servlet-2");
           resp.getWriter().flush();
           resp.getWriter().close();
       }
   
       @Override
       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           doGet(req, resp);
       }
   }
   ```

2. 定义配置类

   ```java
   @Configuration  //该注解将此类定义为配置类(相当一个xml配置文件)
   public class ServletConfig {
   
       //@Bean是一个方法级别上的注解,主要用在配置类里
       //相当于一个
       // <beans>
       //      <bean id="" class="">
       // </beans>
       @Bean
       public ServletRegistrationBean myServletRegistrationBean() {
           ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(new MyServlet(),"/myservlet");
   
           return servletRegistrationBean;
       }
   }
   ```

   

## SpringBoot过滤器

#### 方法一

###### 步骤

1. 创建filter类实现Filter接口,添加@WebFilter注解

   ```Java
   @WebFilter(urlPatterns = "/myfilter")
   public class MyFilter implements Filter {
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("-------------------您已进入过滤器---------------------");
           filterChain.doFilter(servletRequest, servletResponse);
       }
   }
   ```

2. 扫描@WebFilter注解

   ```Java
   @SpringBootApplication
   @ServletComponentScan(basePackages = "com.bjpowernode.springboot.filter")
   public class Application {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   }
   ```

#### 方法二

###### 步骤

1. 创建Filter类实现Filter接口

   ```Java
   public class MyFilter implements Filter {
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           System.out.println("-------------------您已进入过滤器-222-------------------");
           filterChain.doFilter(servletRequest, servletResponse);
       }
   }
   ```

2. 定义配置类

   ```java
   @Configuration  //定义此类为配置类
   public class FilterConfig {
       @Bean
       public FilterRegistrationBean myFilterRegistrationBean() {
   
           //注册过滤器
           FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new MyFilter());
   
           //添加过滤路径
           filterRegistrationBean.addUrlPatterns("/user/*");//不能使用/user/**,需要一个一个指定
   
           return filterRegistrationBean;
       }
   }
   ```

   

## SpringBoot项目配置字符编码

#### 方法一:

###### 步骤:

1. 创建配置类,设置字符编码过滤器

   ```Java
   @Configuration  //将此类定义为配置文件
   public class SystemConfig {
   
       @Bean
       public FilterRegistrationBean characterEncodingFilterRegistrationBean() {
   
           //创建字符编码过滤器
           CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
           //设置强制使用指定字符编码
           characterEncodingFilter.setForceEncoding(true);
           //设置指定字符编码
           characterEncodingFilter.setEncoding("UTF-8");
   
   
           FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
   
           //设置字符编码过滤器
           filterRegistrationBean.setFilter(characterEncodingFilter);
           //设置字符编码过滤器路径
           filterRegistrationBean.addUrlPatterns("/*");
   
           return filterRegistrationBean;
       }
   }
   ```

2. 在配置文件中关闭Springboot的http字符编码的支持

   ```properties
   #在配置文件中关闭Springboot的http字符编码的支持
   #只有关闭该选项后,Springboot字符编码过滤器才生效
   spring.http.encoding.enabled=false
   ```

#### 方式二

###### 步骤

- 在配置文件中配置SpringBoot字符编码格式

  ```properties
  spring.http.encoding.enabled=true
  spring.http.encoding.force=true
  spring.http.encoding.charset=utf-8
  ```

## SpringBoot的打包部署

#### war包

###### 步骤

1. 程序入口类需扩展继承 SpringBootServletInitializer类并覆盖 configure 方法 

   ```java
   @SpringBootApplication
   public class Application extends SpringBootServletInitializer {
   
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   
       @Override
       protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
   
           //参数为当前springboot启动类
           //构造新资源
           return builder.sources(Application.class);
       }
   }
   ```

2. 在 pom.xml 中添加（修改）打包方式为 war

   ```xml
    <!--修改打包方式-->
       <packaging>war</packaging>
   ```

3. 在 pom.xml 中配置将配置文件编译到类路径

   ```xml
           <resources>
               <resource>
                   <!--源文件夹-->
                   <directory>src/main/webapp</directory>
                   <!--目标文件夹-->
                   <targetPath>META-INF/resources</targetPath>
                   <includes>
                       <!--包含的文件-->
                       <include>*.*</include>
                   </includes>
               </resource>
   <!--src/main/resources下的所有配置文件编译到classes下面去-->
               <resource>
                   <directory>src/main/resources</directory>
                   <includes>
                       <include>**/*.*</include>
                   </includes>
               </resource>
           </resources>
   ```

4. 在 pom.xml 的 build 标签下通过 finalName 指定打 war包的名字

   ```
    <!--指定打war包的字符-->
           <finalName>springboot</finalName>
   ```

- springBoot打war包,部署到tomcat中,之前配置的上下文根和端口号就失效了,要以本地tomcat为准

#### jar包

- 默认 SpingBoot 提供的打包插件版本为 2.2.2.RELEASE，这个版本打的 jar 包 jsp 不能访问，我们这里修改为 1.4.2.RELEASE（其它版本测试都有问题） 

  ```xml
  	<plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
                  <version>1.4.2.RELEASE</version>
              </plugin>
      </plugins>
  ```

- jar包以之前配置的上下文根和端口号为准

## SpringBoot集成日志

#### 步骤

1. 编写SpringBoot集成SSM

2. 在src/main/resources中添加日志配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
   <!-- scan:当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true -->
   <!-- scanPeriod:设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
   <!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。通常不打印 -->
   <configuration scan="true" scanPeriod="10 seconds">
       <!--输出到控制台-->
       <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
           <!--此日志appender是为开发使用，只配置最底级别，控制台输出的日志级别是大于或等于此级别的日志信息-->
           <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
               <level>debug</level>
           </filter>
           <encoder>
               <Pattern>%date [%-5p] [%thread] %logger{60} [%file : %line] %msg%n</Pattern>
               <!-- 设置字符集 -->
               <charset>UTF-8</charset>
           </encoder>
       </appender>
       <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
           <!--<File>/home/log/stdout.log</File>-->
           <File>D:/log/stdout.log</File>
           <encoder>
               <pattern>%date [%-5p] %thread %logger{60}[%file : %line] %msg%n</pattern>
           </encoder>
           <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
               <!-- 添加.gz 历史日志会启用压缩 大大缩小日志文件所占空间 -->
               <!--<fileNamePattern>/home/log/stdout.log.%d{yyyy-MM-dd}.log</fileNamePattern>-->
               <fileNamePattern>D:/log/stdout.log.%d{yyyy-MM-dd}.log</fileNamePattern>
               <maxHistory>30</maxHistory><!--  保留30天日志 -->
           </rollingPolicy>
       </appender>
       <logger name="com.abc.springboot.mapper" level="DEBUG"/>
       <root level="INFO">
           <appender-ref ref="CONSOLE"/>
           <appender-ref ref="FILE"/>
       </root>
   </configuration> 
   ```

3. 添加lombok依赖

   ```xml
   		<dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
           </dependency>
   ```

4. 配置日志注解

   - 之前我们大多数时候自己在每个类创建日志对象去打印信息，比较麻烦： private static final Logger logger = LoggerFactory.getLogger(StudentServiceImpl.class); logger.error("xxx"); 
     现在可以直接在类上通过 @Slf4j 标签去声明式注解日志对象 

   ```java
   @Controller
   @Slf4j
   public class StudentController {
   
       @Autowired
       private StudentService studentService;
       private Logger log1;
   
       @RequestMapping(value = "/student/count")
       public @ResponseBody String studentCount() {
   
           log.trace("查询当前学生总人数");
           log.debug("查询当前学生总人数");
           log.info("查询当前学生总人数");
           log.warn("查询当前学生总人数");
           log.error("查询当前学生总人数");
   
           Integer studentCount = studentService.queryStudentCount();
   
           return "学生总人数为:" + studentCount;
       }
   }
   ```

   

## SpringBoot集成thymeleaf

#### 步骤

1. 在pom.xml中添加thymeleaf

   ```xml
    <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-thymeleaf</artifactId>
           </dependency>
   ```

2. 编写Controller类

   ```java
   @Controller
   public class MyController {
       @RequestMapping(value = "/some")
       public String show(Model model){
           model.addAttribute("mydata", "istarve");
           //html文件名
           return "aaa";
       }
   }
   ```

3. 编写html文件

   ```html
   <!DOCTYPE html>
   <!--xmlns:th="http://www.thymeleaf.org"  获取thymeleaf后台属性-->
   <html lang="en" xmlns:th="http://www.thymeleaf.org">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
   </head>
   <body>
       <!--thymeleaf模版引擎的页面必须得通过中央调度器-->
   <h2 th:text="${mydata}">yeke</h2>
   </body>
   </html>
   ```

   

#### thymeleaf路径配置

```properties
#不用配置也可以工作，因为基本 Thymeleaf 的配置都有默认值
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

#### thymeleaf更改前端页面时不用重启服务器

```properties
#thymeleaf页面的缓存开关，默认true开启缓存 
#建议在开发阶段关闭thymeleaf页面缓存，目的实时看到页面 
spring.thymeleaf.cache=false 
```

然后更改配置

<img src="https://i.loli.net/2021/10/02/7T3LRQlGsKqA9iJ.png" alt="image-20211002134152204" style="zoom: 33%;" />

#### thymeleaf表达式

###### 标准变量表达式

```html
<h1>标准变量表达式:${} -> (推荐)</h1>
用户编号:<span th:text="${user.id}"></span><br/>
用户姓名:<span th:text="${user.username}"></span><br/>
用户年龄:<span th:text="${user.age}"></span><br/>

<h1>选择变量表达式(星号表达式):*{} -> (不推荐)</h1>
<!--
    *{}必须使用th:object属性来绑定这个对象
    在div子标签中使用*来代替绑定的对象${user}
-->
<div th:object="${user}">
    用户编号:<span th:text="*{id}"></span><br/>
    用户姓名:<span th:text="*{username}"></span><br/>
    用户年龄:<span th:text="*{age}"></span><br/>
</div>

<h1>标准变量表达式与选择变量表达式的混合使用(不推荐)</h1>
用户编号<span th:text="*{user.id}"></span><br/>
用户年龄<span th:text="*{user.age}"></span><br/>
用户姓名<span th:text="*{user.username}"></span><br/>
```

###### url路径表达式

```html
<h1>URL路径表达式:@{....}</h1>
<h2>a标签中的绝对路径(没有参数)</h2>
<a href="http://www.baidu.com">传统写法:跳转至百度</a><br/>
<a th:href="@{http://www.bjpowernode.com}">路径表达式:路径到动力节点</a><br/>
<a th:href="@{http://localhost:8080/user/detail1}">跳转至:/user/detail1</a><br/>
<a href="http://localhost:8080/user/detail1">传统写法跳转至:/user/detail1</a><br/>

<h2>URL路径表达式,相对路径[没有参数](实际开发中推荐使用的)</h2>
<a th:href="@{/user/detail1}">跳转至:/user/detail1</a><br/>

<h2>绝对路径(带参数)(不推荐使用)</h2>
<a href="http://localhost:8080/test?username='zhangsan'">绝对路径,带参数:/test,并带参数username</a><br/>
<a th:href="@{http://localhost:8080/test?username=zhangsan}">路径表达工写法,带参数:/test,并带参数username</a><br/>

<h2>相对路径(带参数)</h2>
<a th:href="@{/test?username=lisi}">相对路径,带参数</a>

<h2>相对路径(带参数:后台获取的参数值)</h2>
<!--/test?username=1001-->
<a th:href="@{'/test?username='+${id}}">相对路径:获取后台参数值</a>

<h2>相对路径(带多个参数:后台获取的参数值)</h2>
<!--
    /test1?id=1001&username=zhaoliu&age=28
-->
<a th:href="@{'/test1?id='+${id}+'&username='+${username}+'&age='+${age}}">相对路径(带多个参数:后台获取的参数值)</a>
<a th:href="@{/test1(id=${id},username=${username},age=${age})}">强烈推荐使用:@{}相对路径(带多个参数:后台获取的参数值)</a><br/>
<!--RESTful风格只能拼接方式-->
<a th:href="@{'/test2/'+${id}}">请求路径为RESTful风格</a><br/>

<a th:href="@{'/test3/'+${id}+'/'+${username}}">请求路径为RESTful风格</a><br/>
```

- 前端需要从后端取值时用`th:`,不需要取值时可用可不用

###### th:each

```java
@Controller
public class UserController {

    @RequestMapping("/each/list")
    public String eachList(Model model) {
        List<User> userList = new ArrayList<User>();

        for (int i = 0; i < 10; i++) {
            User user = new User();
            user.setId(100 + i);
            user.setNick("张" + i);
            user.setPhone("1361234567" + i);
            user.setAddress("北京市大兴区" + i);
            userList.add(user);
        }
        model.addAttribute("userList", userList);
        model.addAttribute("data", "SpringBoot");
        return "eachList";
    }

    @RequestMapping(value = "/each/map")
    public String eachMap(Model model) {
        Map<Integer, Object> userMaps = new HashMap<Integer, Object>();

        for (int i = 0; i < 10; i++) {
            User user = new User();
            user.setId(i);
            user.setNick("李四" + i);
            user.setPhone("1390000000" + i);
            user.setAddress("天津市" + i);
            userMaps.put(i, user);
        }
        model.addAttribute("userMaps", userMaps);
        return "eachMap";
    }

    @RequestMapping(value = "/each/array")
    public String eachArray(Model model) {

        User[] userArray = new User[10];

        for (int i = 0; i < 10; i++) {

            User user = new User();
            user.setId(i);
            user.setNick("赵六" + i);
            user.setPhone("1380000000" + i);
            user.setAddress("深圳市" + i);
            userArray[i] = user;

        }
        model.addAttribute("userArray", userArray);
        return "eachArray";
    }

    @RequestMapping(value = "/each/all")
    public String eachAll(Model model) {
        //list -> Map -> List -> User
        List<Map<Integer, List<User>>> myList = new ArrayList<Map<Integer, List<User>>>();

        for (int i = 0; i < 2; i++) {

            Map<Integer, List<User>> myMap = new HashMap<Integer, List<User>>();
            for (int j = 0; j < 2; j++) {
                List<User> myUserList = new ArrayList<User>();
                for (int k = 0; k < 3; k++) {
                    User user = new User();
                    user.setId(k);
                    user.setNick("张三" + k);
                    user.setPhone("1350000000" + k);
                    user.setAddress("广州市" + i);
                    myUserList.add(user);
                }
                myMap.put(j, myUserList);
            }
            myList.add(myMap);

        }
        model.addAttribute("myList", myList);
        return "eachAll";
    }

}
```

```html
<!--
	List循环遍历

    user 当前循环的对象变量名称(随意)
    userStat 当前循环对象状态的变量(可选默认就是对象变量名称+Stat)
    ${userList} 当前循环的集合
-->
<div th:each="user,userStat:${userList}">
    <span th:text="${userStat.index}"></span>
    <span th:text="${userStat.count}"></span>
    <span th:text="${user.id}"></span>
    <span th:text="${user.nick}"></span>
    <span th:text="${user.phone}"></span>
    <span th:text="${user.address}"></span>
</div>

<div></div>
<div th:each="user:${userList}">
    <span th:text="${userStat.index}"></span>
    <span th:text="${userStat.count}"></span>
    <span th:text="${user.id}"></span>
    <span th:text="${user.nick}"></span>
    <span th:text="${user.phone}"></span>
    <span th:text="${user.address}"></span>
</div>

<h1 th:text="${data}">xxxx</h1>
<div th:text="${data}">xxxx</div>
<span th:text="${data}">xxxx</span>
<input type="text" th:value="${data}"/>
```

```html
<!--
	map循环遍历

    Map集合结构
    key     value
    0       user
    1       user
    2       user
    3       user
    ..      ...
-->
<div th:each="userMap,userMapStat:${userMaps}">
    <span th:text="${userMapStat.count}"></span>
    <span th:text="${userMapStat.index}"></span>
    <span th:text="${userMap.key}"></span>
    <span th:text="${userMap.value}"></span>
    <span th:text="${userMap.value.id}"></span>
    <span th:text="${userMap.value.nick}"></span>
    <span th:text="${userMap.value.phone}"></span>
    <span th:text="${userMap.value.address}"></span>
</div>
<div>

</div>

<div th:each="userMap:${userMaps}">
    <span th:text="${userMapStat.count}"></span>
    <span th:text="${userMapStat.index}"></span>
    <span th:text="${userMap.key}"></span>
    <span th:text="${userMap.value}"></span>
    <span th:text="${userMap.value.id}"></span>
    <span th:text="${userMap.value.nick}"></span>
    <span th:text="${userMap.value.phone}"></span>
    <span th:text="${userMap.value.address}"></span>
</div>
```

```html
<h1>循环遍历Array数组(使用方法同list一样)</h1>
<div th:each="user,userStat:${userArray}">
    <span th:text="${userStat.index}"></span>
    <span th:text="${userStat.count}"></span>
    <span th:text="${user.id}"></span>
    <span th:text="${user.nick}"></span>
    <span th:text="${user.phone}"></span>
    <span th:text="${user.address}"></span>
</div>
```

###### 条件判断

```java
@Controller
public class UserController {

    @RequestMapping(value = "/condition")
    public String condition(Model model) {

        model.addAttribute("sex",1);

        model.addAttribute("flag",true);

        model.addAttribute("productType",0);


        return "condition";
    }
}
```

```html
<h1>th:if 用法:如果满足条件显示(执行),否则相反</h1>
<div th:if="${sex eq 1}">
    男
</div>
<div th:if="${sex eq 0}">
    女
</div>

<h1>th:unless 用法:与th:if用法相反,即条件判断取反</h1>
<div th:unless="${sex ne 1}">
    女
</div>

<h1>th:switch/th:case用法</h1>
<div th:switch="${productType}">
    <span th:case="0">产品0</span>
    <span th:case="1">产品1</span>
    <span th:case="*">无此产品</span>
</div>
```



###### th:inline

```java
@Controller
public class UserController {

    @RequestMapping(value = "/inline")
    public String inline(Model model) {
        model.addAttribute("data","SpringBoot inline");
        return "inline-test";
    }
}
```



```html
<h1>内敛文本: th:inline="text"</h1>
<div th:inline="text">

    数据:[[${data}]]

</div>


<h1>内敛脚本 th:inline="javascript"</h1>
<script type="text/javascript"  th:inline="javascript">
    function showData() {
        alert([[${data}]]);
        alert("----");
    }
</script>
<button th:onclick="showData()">展示数据</button>
```

###### 字面量

```java
@Controller
public class UserController {


    @RequestMapping(value = "/literal")
    public String literal(Model model) {

        model.addAttribute("sex",1);
        model.addAttribute("data","SpringBoot Data");
        model.addAttribute("flag",true);

        User user = new User();
        user.setId(1001);
        user.setUsername("lisi");
        model.addAttribute("user",user);

        User userDetail = new User();
        model.addAttribute("userDetail",userDetail);

        return "literal";
    }

    @RequestMapping(value = "/splice")
    public String splice(Model model) {
        model.addAttribute("totalRows",123);
        model.addAttribute("totalPage",13);
        model.addAttribute("currentPage",2);

        return "splice";
    }
}
```

```html
h1>文本字面量,用单引号'....'的字符串就是字面量</h1>
<a th:href="@{'/user/detail?sex=' + ${sex}}">查看性别</a>
<span th:text="Hello"></span>

<h1>数字字面量</h1>
今年是<span th:text="2020">1949</span>年<br/>
20年后是<span th:text="2020+20">1969</span>年<br/>

<h1>boolean字面量</h1>
<div th:if="${flag}">
    执行成功
</div>
<div th:if="${!flag}">
    不成功
</div>

<h1>null字面量</h1>
<span th:text="${user.id}"></span>
<div th:unless="${userDetail eq null}">
    对象已创建,地址不为空
</div>

<div th:if="${userDetail.id eq null}">
    空
</div>
```

###### 字符串拼接

```html
<body>
<span th:text="'共'+${totalRows}+'条'+${totalPage}+'页,当前第'+${currentPage}+'页,首页 上一页 下一页 尾页'">共120条12页,当前第1页,首页 上一页 下一页 尾页</span>

<h1>使用更优雅的方式来拼接字符串: |要拼接的字符串内容|</h1>

<span th:text="|共${totalRows}条${totalPage}页,当前第${currentPage}页,首页 上一页 下一页 尾页|">共120条12页,当前第1页,首页 上一页 下一页 尾页</span>
</body>
```

###### 运算符

```html
<!--
    三元运算 ：表达式?” 正确结果”:” 错误结果”
    算术运算： ：+ , - , * , / , %
    关系比较: ：> , < , >= , <= ( gt , lt , ge , le )
    相等判断： ：== , != ( eq , ne )

    ne -> not equal
    eq -> equal
    ge -> great equal
    le -> little equal
    gt -> great
    lt -> little
-->
<h1>三元运算符 表达式?正确:错误</h1>
<div th:text="${sex eq 1 ? '男':'女'}"></div>
<div th:text="${sex == 1 ? '男':'女'}"></div>

<h1>算术运算</h1>
20+5=<span th:text="20+5"></span><br/>
20-5=<span th:text="20-5"></span><br/>
20*5=<span th:text="20*5"></span><br/>
20/5=<span th:text="20/5"></span><br/>
20%3=<span th:text="20%3"></span><br/>

<h1>关系比较</h1>
5>2为<span th:if="5 gt 2">真</span><br/>
5>2为<span th:if="5 > 2">真</span><br/>
5<2<span th:unless="5 lt 2">真</span><br/>
5<2<span th:unless="5 < 2">真</span><br/>
1>=1<span th:if="1 ge 1">真</span><br/>
1>=1<span th:if="1 >= 1">真</span><br/>
1<=1<span th:if="1 le 1">真</span><br/>
1<=1<span th:if="1 <= 1">真</span><br/>

<h1>相等判断</h1>
<span th:if="${sex == 1}">男</span>
<span th:if="${sex eq 1}">男</span>
<span th:unless="${sex ne 1}">女</span>
```



###### Thymaleaf 表达式基本对象 

```java
@Controller
public class UserController {

    @RequestMapping(value = "/index")
    public String index(HttpServletRequest request,Model model,Integer id) {
        model.addAttribute("username","lisi");

        request.getSession().setAttribute("data","sessionData");

        return "index";
    }
}
```



```html
<h1>从SESSION中获取值</h1>
<span th:text="${#session.getAttribute('data')}"></span><br/>
<span th:text="${#httpSession.getAttribute('data')}"></span><br/>
<span th:text="${session.data}"></span><br/>

<script type="text/javascript" th:inline="javascript">
    // http://localhost:8080/springboot/user/detail
    //获取协议名称
    var scheme = [[${#request.getScheme()}]];

    //获取服务器名称
    var serverName = [[${#request.getServerName()}]];

    //获取服务器端口号
    var serverPort = [[${#request.getServerPort()}]];

    //获取上下文根
    var contextPath = [[${#request.getContextPath()}]];

    var allPath = scheme + "://" +serverName+":"+serverPort+contextPath;

    var requestURL = [[${#httpServletRequest.requestURL}]];
     //获取请求参数
    var queryString = [[${#httpServletRequest.queryString}]];

    var requestAddress = requestURL + "?" +queryString;

    alert(requestAddress);


</script>
```

###### Thymeleaf 表达式功能对象

```html
<!--内置功能对象前都需要加#号，内置对象一般都以 s 结尾 -->
<body>
<div th:text="${time}"></div>
<div th:text="${#dates.format(time,'yyyy-MM-dd HH:mm:ss')}"></div>
<div th:text="${data}"></div>
<div th:text="${#strings.substring(data,0,10)}"></div>
<div th:text="${#lists}"></div>
</body>
```

