##  springMVC概述

#### springMVC定义

是spring的一个模块,专门做web开发的,相当于servlet的升级,底层是servlet,框架是在servlet的基础上加入了一些功能

#### spring和springMVC对比

- spring:spring是容器,ioc能管理对象
- springMVC:能够创建对象,放入到容器中(springMVC容器),springMVC容器中放的是控制器对象

#### springMVC简要流程

- 使用@Controller创建控制器对象,把对象放入到springMVC容器中,把创建的对象作为控制器使用。这个控制器对象能够接收用户的请求，显示处理结果，就当作是一个servlet使用。

- 使用 @Controller 注解创建的是一个普通的对象 ，不是servlet，springmvc赋予了控制器对象一些额外的功能
- web开发底层是servlet，springmvc中有一个对象是servlet：DispatcherServlet（中央调度器）
  - DispatcherServlet：负责接收用户的所有请求，用户把请求给了DispatcherServlet，之后DispatcherServlet把请求转发给我们的Controller对象，最后Controller对象处理请求。
  - DispatcherServlet叫做中央调度器， 是一个servlet， 它的父类是继承HttpServlet
  - DispatcherServlet页叫做前端控制器（front controller）

#### springMVC具体开发步骤

![image-20210920102135214](https://i.loli.net/2021/09/20/sapVbo4AYdWSvtC.png)

1. 新建web maven工程

2. 加入依赖

   1. Spring-webmvc依赖,间接把spring依赖加入到项目
   2. jsp,servlet依赖

3. 在web.xml中注册springMVC框架的核心对象DispatcherServlet

   ```xml
   <servlet>
       	<!--注册springMVC框架的核心对象DispatcherServlet-->
           <servlet-name>myweb</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
   
           <!--自定义springmvc读取的配置文件的位置-->
           <init-param>
               <!--springmvc创建容器对象时，读取的配置文件默认是/WEB-INF/<servlet-name>-servlet.xml    不方便建议自己配置文件位置-->
               <!--springmvc的配置文件的位置的属性-->
               <param-name>contextConfigLocation</param-name>
               <!--指定自定义文件的位置-->
               <param-value>classpath:springmvc.xml</param-value>
           </init-param>
   
           <load-on-startup>1</load-on-startup>
       </servlet>
   
       <servlet-mapping>
           <servlet-name>myweb</servlet-name>
           <url-pattern>*.do</url-pattern>
       </servlet-mapping>
   
   ```

   

4. 创建一个发起请求的页面index.jsp

5. 创建控制器类

   1. 在类上面加入@Controller注解,创建对象,并放入到SpringMVC容器中
   2. 在类中的方法上面加入@RequestMapping注解

   ```java
   @Controller
   public class MyController {
       @RequestMapping(value = "/some")
       public ModelAndView doSome(HttpServletRequest request , String name, Integer age){
           System.out.println("doSome name="+name+"   age="+age);
           ModelAndView mv = new ModelAndView();
           mv.addObject("myname",name);
           mv.addObject("myage",age);
           mv.setViewName("show");
   
          // request.getRequestDispatcher("/show.jsp").forward(request,null);
           return mv;
       }
   ```

   

6. 创建一个作为结果的jsp,显示请求的处理结果

7. 创建springMVC的配置文件(spring的配置文件一样)

   声明组件扫描器,指定@Controller注解所在的包名

   声明视图解析器,帮助处理视图
   
   ```xml
    <!--声明组件扫描器-->
       <context:component-scan base-package="com.bjpowernode.controller" />
   
       <!--声明 springmvc框架中的视图解析器， 帮助开发人员设置视图文件的路径-->
       <bean  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!--前缀：视图文件的路径-->
           <property name="prefix" value="/WEB-INF/view/" />
           <!--后缀：视图文件的扩展名-->
           <property name="suffix" value=".jsp" />
       </bean>
   ```
   
   

###### DispatcherServlet对象的实例作用

- 创建springmvc容器对象
- 读取springmvc的配置文件，把这个配置文件中的对象都创建好

###### @Controller注解

- 定义:创建处理器对象
- 位置:类的上面
- 属性:value 自定义名称<!--一般不写-->

## @RequestMapping

- 定义:请求映射,作用是把一个请求地址和一个方法绑定在一起。一个请求指定一个方法处理。
- 属性:
  1. value是一个String,表示请求uri地址<!--some.do-->值必须唯一,在使用式推荐地址以"/"开头
  2. method取值为RequestMethod枚举常量
     - RequestMethod.GET 为get提交
     - RequestMethod.POST为post提交
- 位置
  - 方法上面
  - 类的上面
    - 表示所有请求地址的公共部分,叫做模块名称

说明 : 使用@RequestMapping修饰的方法可以处理请求的，类似Servlet中的doGet, doPost

#### 处理器方法的参数

处理器方法可以 包含以下四类参数，这些参数会在系统调用时由系统自动赋值，即程序员可在方法内直接使用。

- HttpServletRequest

- HttpServletResponse

- HttpSession

- 请求中所携带的请求参数

  

###### 逐个接收

-  请求中参数名和处理器方法的形参名一样
  - 要求:处理器（控制器）方法的形参名和请求中参数名必须一致。同名的请求参数赋值给同名的形参

![image-20210827215531725](https://i.loli.net/2021/08/27/MYTtIvlEpXkxaiB.png)

###### 解决中文乱码问题

```xml
<!--注册声明过滤器，解决post请求乱码的问题-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <!--设置项目中使用的字符编码-->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <!--强制请求对象（HttpServletRequest）使用encoding编码的值-->
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <!--强制应答对象（HttpServletResponse）使用encoding编码的值-->
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <!--
           /*:表示强制所有的请求先通过过滤器处理。
        -->
        <url-pattern>/*</url-pattern>
    </filter-mapping>	
```



###### 校正请求参数名 @RequestParam

- @RequestParam逐个接收请求参数中， 解决请求中参数名形参名不一样的问题

  - 属性
    1. value 请求中的参数名称
    2. required 是一个boolean，默认是true <!--true表示请求中必须包含此参数。false表示可以为空-->
  - 位置:在处理器方法的形参定义的前面

  ![image-20210827220558026](https://i.loli.net/2021/08/27/ycTW1Y5ksNpOI8e.png)

###### 对象参数接收

- 步骤
  1. 定义实体类      getter and setter  toString()
  2. 修改处理器类MyController
     - 处理器方法形参是java对象， 这个对象的属性名和请求中参数名一样的
  3. 修改show页面

![image-20210827221800460](https://i.loli.net/2021/08/27/dnYDlPMiqehURvH.png)

![image-20210827221810465](https://i.loli.net/2021/08/27/Ze4AJctEvxVsi9a.png)

![image-20210827221822701](https://i.loli.net/2021/08/27/Khy9UVEHPdc2tIQ.png)

#### 处理器方法的返回值

- 使用@controller注解的处理器的处理方法的返回值
  1. ModelAndView
  2. String
  3. void
  4. 自定义类型的返回值

###### ModelAndView

- 有数据有视图,对视图执行forward

```java
@RequestMapping(value = "/ss.do")
public ModelAndView modelAndView(){
    final ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("msg", "msg<------>");
    modelAndView.addObject("fun", "fun<------>");
    modelAndView.setViewName("/show");
    return modelAndView;
}
```

###### String

- 表示视图,可以通过在方法中加入HttpServletRequest参数去设置数据

```java
@RequestMapping(value = "/other")
public String string(HttpServletRequest request){
    request.setAttribute("msg","msg的other");
    return "other";
}
```

###### void

- 不能表示数据也不能表示视图

- 在处理Ajax的时候,可以使用void返回值通过HttpServletResponse输出数据.响应Ajax请求.Ajax请求服务器端返回的就是数据,和视图无关

```Java
 //处理器方法返回void， 响应ajax请求
    //手工实现ajax，json数据： 代码有重复的 1. java对象转为json； 2. 通过HttpServletResponse输出json数据
    @RequestMapping(value = "/returnVoid-ajax.do")
    public void doReturnVoidAjax(HttpServletResponse response, String name, Integer age) throws IOException {
        System.out.println("===doReturnVoidAjax====, name="+name+"   age="+age);
       //处理ajax， 使用json做数据的格式
       //service调用完成了， 使用Student表示处理结果
        Student student  = new Student();
        student.setName("张飞同学");
        student.setAge(28);

        String json = "";
        //把结果的对象转为json格式的数据
        if( student != null){
            ObjectMapper om  = new ObjectMapper();
            json  = om.writeValueAsString(student);
            System.out.println("student转换的json===="+json);
        }

        //输出数据，响应ajax的请求
        response.setContentType("application/json;charset=utf-8");
        PrintWriter pw  = response.getWriter();
        pw.println(json);
        pw.flush();
        pw.close();

    }

```

###### Object

- 返回Object表示数据,和视图无关 可以使用对象表示数据,响应Ajax 

- 步骤

  1. 导入Jackson相关依赖  由于返回object数据, 一般都是将数据转化为sjon对象后传递给浏览器页面,Jackson工具的作用就是将object转化为json

  2. 在springmvc配置文件中加入<mvc:annotation-driven>注解驱动

     ```xml
     <mvc:annotation-driven/>
     ```

     ```Java
     等价步骤
     json  = om.writeValueAsString(student);
     ```

     

  3. .在处理器方法的上面加入@ResponseBody注解

     ```Java
     等价步骤
      response.setContentType("application/json;charset=utf-8");
      PrintWriter pw  = response.getWriter();
      pw.println(json);
     ```

     @ResponseBody
     
     - 在控制层（controller）的方法上,类上
     - 作用：将方法的返回值，以特定的格式写入到response的body区域，进而将数据返回给客户端。

-   springmvc处理器方法返回Object， 可以转为json输出到浏览器，响应ajax的内部原理

  - <mvc: annotation-driven>注解驱动

  注解驱动实现的功能是:完成Java对象到json,xml,text,二进制等数据格式的转换

  <mvc: annotation-driven>在加入到springmvc配置文件后，会自动创建HttpMessageConverter接口的7个实现类对象,包括 MappingJackson2HttpMessageConverter （使用jackson工具库中的ObjectMapper实现java对象转为json字符串）

  | HttpMessageConverter 接口实现类          | 作用                                                         |
  | ---------------------------------------- | ------------------------------------------------------------ |
  | ByteArrayHttpMessageConverter            | 负责读取二进制格式的数据和写出二进制格式的数据               |
  | **StringHttpMessageConverter**           | **负责读取字符串格式的数据和写出字符串格式的数据**           |
  | ResourceHttpMessageConverter             | 负责读取资源文件和写出资源文件数据                           |
  | SourceHttpMessageConverter               | 能够读/ 写来自 HTTP 的请求与响应的javax.xml.transform.Source ,支持 DOMSource, SAXSource, 和 StreamSource 的XML 格式 |
  | AllEncompassingFormHttpMessageConvert er | 负责处理表单(form)数据                                       |
  | Jaxb2RootElementHttpMessageConverter     | 使用 JAXB 负责读取和写入xml 标签格式的数据                   |
  | **MappingJackson2HttpMessageConverter**  | **负责读取和写入 json 格式的数据。利用Jackson 的ObjectMapper 读写 json 数据，操作Object 类型数据，可读取application/json，响应媒体类型为 application/json** |

  -  HttpMessageConverter接口：消息转换器。
         功能：定义了java转为json，xml等数据格式的方法。 这个接口有很多的实现类。
               这些实现类完成 java对象到json， java对象到xml，java对象到二进制数据的转换
    - 下面的两个方法是控制器类把结果输出给浏览器时使用的：
           boolean canWrite(Class<?> var1, @Nullable MediaType var2);
           void write(T var1, @Nullable MediaType var2, HttpOutputMessage var3)

  ```java
   例如处理器方法
       @RequestMapping(value = "/returnString.do")
       public Student doReturnView2(HttpServletRequest request,String name, Integer age){
               Student student = new Student();
               student.setName("lisi");
               student.setAge(20);
               return student;
       }
  
       1）canWrite作用检查处理器方法的返回值，能不能转为var2表示的数据格式。
         检查student(lisi，20)能不能转为var2表示的数据格式。如果检查能转为json，canWrite返回true
         MediaType：表示数格式的， 例如json， xml等等
  
       2）write：把处理器方法的返回值对象，调用jackson中的ObjectMapper转为json字符串。
          json  = om.writeValueAsString(student);
  
  ```

- @ResponseBody注解

  ```java
     放在处理器方法的上面， 通过HttpServletResponse输出数据，响应ajax请求的。
             PrintWriter pw  = response.getWriter();
             pw.println(json);
             pw.flush();
             pw.close();
  ```

  **例:**

  ```java
   /**
       * 处理器方法返回一个Student，通过框架转为json，响应ajax请求
       * @ResponseBody:
       *    作用：把处理器方法返回对象转为json后，通过HttpServletResponse输出给浏览器。
       *    位置：方法的定义上面。 和其它注解没有顺序的关系。
       * 返回对象框架的处理流程：
       *  1. 框架会把返回Student类型，调用框架的中ArrayList<HttpMessageConverter>中每个类的canWrite()方法
       *     检查那个HttpMessageConverter接口的实现类能处理Student类型的数据--MappingJackson2HttpMessageConverter
       *
       *  2.框架会调用实现类的write（）， MappingJackson2HttpMessageConverter的write()方法
       *    把李四同学的student对象转为json， 调用Jackson的ObjectMapper实现转为json
       *    contentType: application/json;charset=utf-8
       *
       *  3.框架会调用@ResponseBody把2的结果数据输出到浏览器， ajax请求处理完成
       */
      @RequestMapping(value = "/returnStudentJson.do")
      @ResponseBody
      public Student doStudentJsonObject(String name, Integer age) {
          //调用service，获取请求结果数据 ， Student对象表示结果数据
          Student student = new Student();
          student.setName("李四同学");
          student.setAge(20);
          return student; // 会被框架转为json
  
      }
  
  
  ```

  ```java
    /**
       *  处理器方法返回List<Student>
       * 返回对象框架的处理流程：
       *  1. 框架会把返回List<Student>类型，调用框架的中ArrayList<HttpMessageConverter>中每个类的canWrite()方法
       *     检查那个HttpMessageConverter接口的实现类能处理Student类型的数据--MappingJackson2HttpMessageConverter
       *
       *  2.框架会调用实现类的write（）， MappingJackson2HttpMessageConverter的write()方法
       *    把李四同学的student对象转为json， 调用Jackson的ObjectMapper实现转为json array
       *    contentType: application/json;charset=utf-8
       *
       *  3.框架会调用@ResponseBody把2的结果数据输出到浏览器， ajax请求处理完成
       */
      @RequestMapping(value = "/returnStudentJsonArray.do")
      @ResponseBody
      public List<Student> doStudentJsonObjectArray(String name, Integer age) {
  
          List<Student> list = new ArrayList<>();
          //调用service，获取请求结果数据 ， Student对象表示结果数据
          Student student = new Student();
          student.setName("李四同学");
          student.setAge(20);
          list.add(student);
  
          student = new Student();
          student.setName("张三");
          student.setAge(28);
          list.add(student);
  
  
          return list;
  
      }
  ```

  ```Java
   /**
       * 处理器方法返回的是String ， String表示数据的，不是视图。
       * 区分返回值String是数据，还是视图，看有没有@ResponseBody注解
       * 如果有@ResponseBody注解，返回String就是数据，反之就是视图
       *
       * 默认使用“text/plain;charset=ISO-8859-1”作为contentType,导致中文有乱码，
       * 解决方案：给RequestMapping增加一个属性 produces, 使用这个属性指定新的contentType.
       * 返回对象框架的处理流程：
       *  1. 框架会把返回String类型，调用框架的中ArrayList<HttpMessageConverter>中每个类的canWrite()方法
       *     检查那个HttpMessageConverter接口的实现类能处理String类型的数据--StringHttpMessageConverter
       *
       *  2.框架会调用实现类的write（）， StringHttpMessageConverter的write()方法
       *    把字符按照指定的编码处理 text/plain;charset=ISO-8859-1
       *
       *  3.框架会调用@ResponseBody把2的结果数据输出到浏览器， ajax请求处理完成
       */
      @RequestMapping(value = "/returnStringData.do",produces = "text/plain;charset=utf-8")
      @ResponseBody
      public String doStringData(String name,Integer age){
          return "Hello SpringMVC 返回对象，表示数据";
      }
  
  ```

  

#### `<url-pattern/>`

- tomcat本身能处理静态资源的访问， 像html， 图片， js文件都是静态资源
  - tomcat的web.xml文件有一个servlet 名称是 default ， 在服务器启动时创建的。
  - default这个servlet作用
    1. 处理静态资源
    2. 处理未映射到其它servlet的请求。

- 使用框架的时候， url-pattern可以使用两种值

  1. 使用扩展名方式， 语法 *.xxxx , xxxx是自定义的扩展名。 常用的方式 *.do, *.action, *.mvc等等
     不能使用 *.jsp
     http://localhost:8080/myweb/some.do
     http://localhost:8080/myweb/other.do

  2. 使用斜杠 "/"
       当你的项目中使用了  / ，它会替代 tomcat中的default。
       导致所有的静态资源都给DispatcherServlet处理， 默认情况下DispatcherServlet没有处理静态资源的能力。
       没有控制器对象能处理静态资源的访问。所以静态资源（html，js，图片，css）都是404.

       动态资源some.do是可以访问，的因为我们程序中有MyController控制器对象，能处理some.do请求。

###### 第一种处理静态资源的方式

```xml
springmvc配置文件加入 <mvc:default-servlet-handler>
```

- 原理

  加入这个标签后，框架会创建控制器对象DefaultServletHttpRequestHandler（类似我们自己创建的MyController）.
  DefaultServletHttpRequestHandler这个对象可以把接收的请求转发给 tomcat的default这个servlet。

- 优点和缺点

  优点:简洁

  缺点:需要依赖于tomcat的default的servlet

###### 第二种处理静态资源的方式

```xml
 <mvc:resources mapping="/images/**" location="/images/" />
     <!--images/**:表示 images/p1.jpg  , images/user/logo.gif , images/order/history/list.png-->
    <mvc:resources mapping="/html/**" location="/html/" />
    <mvc:resources mapping="/js/**" location="/js/" />	
 <!--使用一个配置语句，指定多种静态资源的访问-->
 <!--<mvc:resources mapping="/static/**" location="/static/" />-->
```

- 原理:

  mvc:resources 加入后框架会创建 ResourceHttpRequestHandler这个处理器对象。让这个对象处理静态资源的访问,不依赖tomcat

- 参数:

  mapping:访问静态资源的uri地址,使用通配符**

  location:静态资源在你项目中的目录位置

## SpringMVC核心技术

#### 请求转发

处理器理器方法返回ModelAndView,实现转发forward
* 语法： setViewName("forward:视图文件完整路径")
* forward特点： 不和视图解析器一同使用，就当项目中没有视图解析器

```Java
@RequestMapping(value = "/doForward.do")
    public ModelAndView doSome(){
        //处理some.do请求了。 相当于service调用处理完成了。
        ModelAndView mv  = new ModelAndView();
        mv.addObject("msg","欢迎使用springmvc做web开发");
        mv.addObject("fun","执行的是doSome方法");
        //显示转发
        mv.setViewName("forward:/WEB-INF/view/show.jsp");
        //和加上视图解析器mv.setViewName(/show)完成一样的效果
        //但显示转发可以转发到不在视图解析器规定的范围
        return mv;
    }
```

#### 重定向

- 语法：setViewName("redirect:视图完整路径")
- redirect特点： 不和视图解析器一同使用，就当项目中没有视图解析器

```Java
  /**框架对重定向的操作：
     * 1.框架会把Model中的简单类型的数据，转为string使用，作为hello.jsp的get请求参数使用。
     *   目的是在 doRedirect.do 和 hello.jsp 两次请求之间传递数据
     *
     * 2.在目标hello.jsp页面可以使用参数集合对象 ${param}获取请求参数值
     *    ${param.myname}
     *
     * 3.重定向不能访问/WEB-INF资源
     */
@RequestMapping(value = "/doRedirect.do")
    public ModelAndView doWithRedirect(String name,Integer age){
        //处理some.do请求了。 相当于service调用处理完成了。
        ModelAndView mv  = new ModelAndView();
        //数据放入到 request作用域
        mv.addObject("myname",name);
        mv.addObject("myage",age);
        //重定向
        //mv.setViewName("redirect:/hello.jsp");
        //http://localhost:8080/ch08_forard_redirect/hello.jsp?myname=lisi&myage=22

        //重定向不能访问/WEB-INF资源
        mv.setViewName("redirect:/WEB-INF/view/show.jsp");
        return mv;
    }
```

#### 异常处理

###### 步骤

1. 新建maven,web项目

2. 加入依赖

3. 新建一个自定义异常类,再定义它的子类

4. 在controller抛出子类异常

   ```Java
   @Controller
   public class MyController {
       @RequestMapping(value = "/some.do")
       public ModelAndView doSome(String name,Integer age) throws MyUserException {
           //处理some.do请求了。 相当于service调用处理完成了。
           ModelAndView mv  = new ModelAndView();
           //try {
               //根据请求参数抛出异常
               if (!"zs".equals(name)) {
                   throw new NameException("姓名不正确！！！");
               }
               if (age == null || age > 80) {
                   throw new AgeException("年龄比较大！！！");
               }
           //}catch(Exception e){
           //   e.printStackTrace();
           //}
           mv.addObject("myname",name);
           mv.addObject("myage",age);
           mv.setViewName("show");
           return mv;
       }
   }
   ```

5. 创建一个普通类，作用全局异常处理类

   1. 在类的上面加入@ControllerAdvice
   2. 在类中定义方法，方法的上面加入@ExceptionHandler

   ```java
   @ControllerAdvice
   public class GlobalExceptionHandler {
       //定义方法，处理发生的异常
       /*
           处理异常的方法和控制器方法的定义一样， 可以有多个参数，可以有ModelAndView,
           String, void,对象类型的返回值
   
           形参：Exception，表示Controller中抛出的异常对象。
           通过形参可以获取发生的异常信息。
   
           @ExceptionHandler(异常的class)：表示异常的类型，当发生此类型异常时，
           由当前方法处理
        */
   
       @ExceptionHandler(value = NameException.class)
       public ModelAndView doNameException(Exception exception){
           //处理NameException的异常。
           /*
              异常发生处理逻辑：
              1.需要把异常记录下来， 记录到数据库，日志文件。
                记录日志发生的时间，哪个方法发生的，异常错误内容。
              2.发送通知，把异常的信息通过邮件，短信，微信发送给相关人员。
              3.给用户友好的提示。
            */
           ModelAndView mv = new ModelAndView();
           mv.addObject("msg","姓名必须是zs，其它用户不能访问");
           mv.addObject("ex",exception);
           mv.setViewName("nameError");
           return mv;
       }
         //处理其它异常， NameException以外，不知类型的异常
       @ExceptionHandler
       public ModelAndView doOtherException(Exception exception){
           //处理其它异常
           ModelAndView mv = new ModelAndView();
           mv.addObject("msg","你的年龄不能大于80");
           mv.addObject("ex",exception);
           mv.setViewName("defaultError");
           return mv;
       	}
   	}
   }
   ```

   

6. 创建处理异常的视图页面

7. 创建springmvc的配置文件

   1. 组件扫描器 ，扫描@Controller注解
   2. 组件扫描器，扫描@ControllerAdvice所在的包名
   3. 声明注解驱动

   ```xml
    <!--声明组件扫描器,扫描@Controller注解-->
   <context:component-scan base-package="com.bjpowernode.controller" /> 
   <!--声明组件扫描器,扫描@ControllerAdvice所在的包名-->
   <context:component-scan base-package="com.bjpowernode.handler" />
   <!--声明注解驱动-->
   <mvc:annotation-driven />
   ```

###### @ControllerAdvice

控制器增强（也就是说给控制器类增加功能--异常处理功能）

- 位置:在类的上面
- 特点：必须让框架知道这个注解所在的包名，需要在springmvc配置文件声明组件扫描器。指定@ControllerAdvice所在的包名

###### @ExceptionHandler

- 位置:方法的上面
- 特点:将一个方法指定为异常处理方法
- 属性:value 为一个 Class<?>数组，用于指定该注解的方法所要处理的异常类，即所要匹配的异常。 

#### 拦截器

- 拦截器是springmvc中的一种对象，需要实现HandlerInterceptor接口。
- 拦截器和过滤器类似，功能方向侧重点不同。 过滤器是用来过滤器请求参数，设置编码字符集等工作。拦截器是拦截用户的请求，做请求做判断处理的。
- 拦截器是全局的，可以对多个Controller做拦截。
  - 一个项目中可以有0个或多个拦截器， 他们在一起拦截用户的请求。
  - 拦截器常用在：用户登录处理，权限检查， 记录日志。

###### 拦截器的使用步骤：

1. 定义类实现HandlerInterceptor接口
2. 在springmvc配置文件中，声明拦截器， 让框架知道拦截器的存在。

###### 拦截器的执行时间:

- 在请求处理之前， 也就是controller类中的方法执行之前先被拦截。
- 在控制器方法执行之后也会执行拦截器。
- 在请求处理完成后也会执行拦截器。

###### 拦截器处理步骤

1. 新建maven web项目

2. 加入依赖

3. 创建Controller类

4. 创建一个普通类,作为拦截器使用

   1. 事项HandlerInterceptor接口

   2. 事项接口中的三个方法

      ```Java
      public class MyInterceptor implements HandlerInterceptor {
      
          @Override
          public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
              btime = System.currentTimeMillis();
              System.out.println("拦截器的MyInterceptor的preHandle()");
              //计算的业务逻辑，根据计算结果，返回true或者false
              //给浏览器一个返回结果
              //request.getRequestDispatcher("/tips.jsp").forward(request,response);
              return true;
          }
      
          @Override
          public void postHandle(HttpServletRequest request,
                                 HttpServletResponse response,
                                 Object handler, ModelAndView mv) throws Exception {
              System.out.println("拦截器的MyInterceptor的postHandle()");
              //对原来的doSome执行结果，需要调整。
              if( mv != null){
                  //修改数据
                  mv.addObject("mydate",new Date());
                  //修改视图
                  mv.setViewName("other");
              }
          }
      
          @Override
          public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                      Object handler, Exception ex) throws Exception {
              System.out.println("拦截器的MyInterceptor的afterCompletion()");
      
              long etime = System.currentTimeMillis();
              System.out.println("计算从preHandle到请求处理完成的时间："+(etime - btime ));
          }
      }
      ```

5. 创建show.jsp

6. 创建springmvc的配置文件

   1. 组件扫描器,扫描@Controller注解

   2. 申明拦截器,并指定拦截请求的uri地址

      ```xml
       <!--声明拦截器： 拦截器可以有0或多个-->
          <mvc:interceptors>
              <!--声明第一个拦截器-->
              <mvc:interceptor>
                  <!--指定拦截的请求uri地址
                      path：就是uri地址，可以使用通配符 **
                            ** ： 表示任意的字符，文件或者多级目录和目录中的文件
                      http://localhost:8080/myweb/user/listUser.do
                      http://localhost:8080/myweb/student/addStudent.do
                  -->
                  <mvc:mapping path="/**"/>
                  <!--声明拦截器对象-->
                  <bean class="com.bjpowernode.handler.MyInterceptor" />
              </mvc:interceptor>
          </mvc:interceptors>
      ```



![image-20210921193414054](https://i.loli.net/2021/09/21/w72e3i6DUY1WFpK.png)

###### preHandle

```java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //计算的业务逻辑，根据计算结果，返回true或者false
        //给浏览器一个返回结果
        //request.getRequestDispatcher("/tips.jsp").forward(request,response);
        return true;
}
```

-  preHandle叫做预处理方法。是整个项目的入口，门户。 当preHandle返回true 请求可以被处理。 preHandle返回false，请求到此方法就截止。
- 参数
  - Object handler ： 被拦截的控制器对象
- 返回值boolean
  -  true：请求是通过了拦截器的验证，可以执行处理器方法。
  - false：请求没有通过拦截器的验证，请求到达拦截器就截止了。 请求没有被处理
- 特点：
  - 方法在控制器方法（MyController的doSome）之前先执行的。用户的请求首先到达此方法
  - 在这个 方法中可以获取请求的信息， 验证请求是否符合要求。可以验证用户是否登录， 验证用户是否有权限访问某个连接地址（url）。如果验证失败，可以截断请求，请求不能被处理。如果验证成功，可以放行请求，此时控制器方法才能执行。

###### postHandle

- postHandle:后处理方法。

```java
 public void postHandle(HttpServletRequest request,HttpServletResponse response,Object handler, ModelAndView mv) throws Exception {
     System.out.println("拦截器的MyInterceptor的postHandle()");
        //对原来的doSome执行结果，需要调整。
        if( mv != null){
            //修改数据
            mv.addObject("mydate",new Date());
            //修改视图
            mv.setViewName("other");
        }
}
```

- 参数 
  - Object handler：被拦截的处理器对象MyController
  - ModelAndView mv:处理器方法的返回值
- 特点：
  - 在处理器方法之后执行的（MyController.doSome()）
  - 能够获取到处理器方法的返回值ModelAndView,可以修改ModelAndView中的数据和视图，可以影响到最后的执行结果。
  - 主要是对原来的执行结果做二次修正

###### afterCompletion

- afterCompletion:最后执行的方法

```java
 @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,Object handler, Exception ex) throws Exception {
        System.out.println("拦截器的MyInterceptor的afterCompletion()");
    }
}
```

- 参数
  - Object handler:被拦截器的处理器对象
  - Exception ex：程序中发生的异常
- 特点:
  - 在请求处理完成后执行的。框架中规定是当你的视图处理完成后，对视图执行了forward。就认为请求处理完成。
  - 一般做资源回收工作的， 程序请求过程中创建了一些对象，在这里可以删除，把占用的内存回收。

###### 多个拦截器

```xml
<!--声明拦截器： 拦截器可以有0或多个
        在框架中保存多个拦截器是ArrayList，
        按照声明的先后顺序放入到ArrayList
    -->
    <mvc:interceptors>
        <!--声明第一个拦截器-->
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <!--声明拦截器对象-->
            <bean class="com.bjpowernode.handler.MyInterceptor" />
        </mvc:interceptor>
        <!--声明第二个拦截器-->
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.bjpowernode.handler.MyInterceptor2" />
        </mvc:interceptor>
    </mvc:interceptors>
```

- 多个拦截器执行顺序

<img src="https://i.loli.net/2021/09/21/AixejGZld7S6kfV.png" alt="image-20210921195445909" style="zoom:50%;" />

###### 拦截器和过滤器的区别

1. 过滤器是servlet中的对象，  拦截器是框架中的对象
2. 过滤器实现Filter接口的对象， 拦截器是实现HandlerInterceptor
3. 过滤器是用来设置request，response的参数，属性的，侧重对数据过滤的。拦截器是用来验证请求的，能截断请求。
4. 过滤器是在拦截器之前先执行的。
5. 过滤器是tomcat服务器创建的对象,拦截器是springmvc容器中创建的对象
6. 过滤器是一个执行时间点。拦截器有三个执行时间点
7. 过滤器可以处理jsp，js，html等等,拦截器是侧重拦截对Controller的对象。 如果你的请求不能被DispatcherServlet接收， 这个请求不会执行拦截器内容
8. 拦截器拦截普通类方法执行，过滤器过滤servlet请求响应

#### SpringMVC执行流程

<img src="https://i.loli.net/2021/09/21/klIrREcX8aY4JF3.png" alt="image-20210921212710609" style="zoom:50%;" />

###### 处理映射器

- 是springmvc框架中的一种对象,框架把实现了HandlerMapping接口的类都叫做映射器(可以有多个)
- 作用:根据请求,从springmvc容器对象中获取处理器对象(MyController controller = ctx.getBean("some.do")),框架把找到的处理器对象放到一个叫做处理器执行链(HandlerExecutionChain)的类保存
- HandlerExecutionChain:保存着处理器对象(MyController),项目中的所有拦截器`List<HandlerInterceptor>`

###### 处理器适配器

- springmvc框架中的对象,需要实现HandlerAdapter接口
- 作用:执行处理器的方法(调用MyController.doSome() 得到返回值ModelAndView)

###### 视图解析器

- springmvc框架中的对象,需要实现ViewResoler接口(可以多个)
- 作用:组成视图完整路径,使用前缀,后缀.并创建View对象.View是一个接口,表示视图,在框架中jsp,html不是string表示,而是使用View和它的实现类表示视图

## SSM整合开发

#### 步骤

1. springdb的MySQL库,表使用student(id,auto_increment, name, age)

2. 新建maven web项目

3. 加入依赖
   - springmvc，spring，mybatis三个框架的依赖，jackson依赖，mysql驱动，druid连接池
       jsp，servlet依赖
   
4. 写web.xml
   1. 注册DispatecherServlet
      - 目的:创建springmvc容器对象,才能创建Controller类对象
      - 目的:创建的是Servlet,才能接受用户的请求
      
      ```xml
      <!--注册中央调度器-->
        <servlet>
          <servlet-name>myweb</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:conf/dispatcherServlet.xml</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
          <servlet-name>myweb</servlet-name>
          <url-pattern>*.do</url-pattern>
        </servlet-mapping>
      ```
      
      
      
   2. 注册spring的监听器:ContextLoaderListener
      - 目的:创建spring的容器对象,才能创建service,dao等对象
   
      ```xml
      <!--注册spring的监听器-->
        <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath:conf/applicationContext.xml</param-value>
        </context-param>
        <listener>
          <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
      ```
   
      
   
   3. 注册字符集过滤器,解决post请求乱码的问题
   
      ```xml
      <!--注册字符集过滤器-->
        <filter>
          <filter-name>characterEncodingFilter</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
          </init-param>
          <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
          </init-param>
          <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
          </init-param>
        </filter>
        <filter-mapping>
          <filter-name>characterEncodingFilter</filter-name>
          <url-pattern>/*</url-pattern>
        </filter-mapping>
      ```
   
      
   
5. 创建包,Controller包， service ，dao，实体类包名创建好

6. 写Springmvc,spring,mybatis的配置文件

   ```xml
    <!--springmvc配置文件， 声明controller和其它web相关的对象-->
       <context:component-scan base-package="com.bjpowernode.controller" />
   
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/" />
           <property name="suffix" value=".jsp" />
       </bean>
   
       <mvc:annotation-driven />
       <!--
         1. 响应ajax请求，返回json
         2. 解决静态资源访问问题。
       -->
   ```

   ```xml
     <!--settings：控制mybatis全局行为-->
      <!-- <settings>
           &lt;!&ndash;设置mybatis输出日志&ndash;&gt;
           <setting name="logImpl" value="STDOUT_LOGGING"/>
       </settings>-->
   
       <!--设置别名-->
       <typeAliases>
           <!--name:实体类所在的包名(不是实体类的包名也可以)-->
           <package name="com.bjpowernode.domain"/>
       </typeAliases>
   
   
       <!-- sql mapper(sql映射文件)的位置-->
       <mappers>
           <!--
             name：是包名， 这个包中的所有mapper.xml一次都能加载
             使用package的要求：
              1. mapper文件名称和dao接口名必须完全一样，包括大小写
              2. mapper文件和dao接口必须在同一目录
           -->
           <package name="com.bjpowernode.dao"/>
       </mappers>
   ```

   ```xml
   <!--spring配置文件： 声明service，dao，工具类等对象-->
   
       <context:property-placeholder location="classpath:conf/jdbc.properties" />
   
       <!--声明数据源，连接数据库-->
       <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
             init-method="init" destroy-method="close">
           <property name="url" value="${jdbc.url}" />
           <property name="username" value="${jdbc.username}" />
           <property name="password" value="${jdbc.password}" />
       </bean>
   
       <!--SqlSessionFactoryBean创建SqlSessionFactory-->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource" />
           <property name="configLocation"  value="classpath:conf/mybatis.xml" />
       </bean>
       
       <!--声明mybatis的扫描器，创建dao对象-->
       <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
           <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
           <property name="basePackage" value="com.bjpowernode.dao" />
       </bean>
   
       <!--声明service的注解@Service所在的包名位置-->
       <context:component-scan base-package="com.bjpowernode.service" />
   
       <!--事务配置：注解的配置， aspectj的配置-->
   ```

   ```properties
   jdbc.url=jdbc:mysql://localhost:3306/springdb
   jdbc.username=root
   jdbc.password=123456
   ```

   

7. 写代码,dao接口和mapper文件,service和实体类,controller,实体类

   ```Java
   //实体类
   public class Student {
   
       private Integer id;
       private String name;
       private Integer age;
   
       public Integer getId() {
           return id;
       }
   
       public void setId(Integer id) {
           this.id = id;
       }
   
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

   ```java
   //dao接口
   public interface StudentDao {
   
       int insertStudent(Student student);
       List<Student> selectStudents();
   }
   ```

   ```xml
   <!--mapper文件-->
   <mapper namespace="com.bjpowernode.dao.StudentDao">
       <select id="selectStudents" resultType="Student">
           select id,name,age from student order by id desc
       </select>
   
       <insert id="insertStudent">
           insert into student(name,age) values(#{name},#{age})
       </insert>
   ```

   ```java
   //servic接口
   public interface StudentService {
   
       int addStudent(Student student);
       List<Student> findStudents();
   }
   //service实现类
   @Service
   public class StudentServiceImpl implements StudentService {
       //引用类型自动注入@Autowired, @Resource
       @Resource
       private StudentDao studentDao;
   
       @Override
       public int addStudent(Student student) {
           int nums = studentDao.insertStudent(student);
           return nums;
       }
   
       @Override
       public List<Student> findStudents() {
           return studentDao.selectStudents();
       }
   }
   ```

   ```java
   //controller类
   @Controller
   @RequestMapping("/student")
   public class StudentController {
   
       @Autowired
       private StudentService service;
   
       //注册学生
       @RequestMapping("/addStudent.do")
       public ModelAndView addStudent(Student student){
           ModelAndView mv = new ModelAndView();
           String tips = "注册失败";
           //调用service处理student
           int nums = service.addStudent(student);
           if( nums > 0 ){
               //注册成功
               tips = "学生【" + student.getName() + "】注册成功";
           }
           //添加数据
           mv.addObject("tips",tips);
           //指定结果页面
           mv.setViewName("result");
           return mv;
   
       }
   
       //处理查询，响应ajax
       @RequestMapping("/queryStudent.do")
       @ResponseBody
       public List<Student> queryStudent(){
           //参数检查， 简单的数据处理
           List<Student> students = service.findStudents();
           return students;
       }
   }
   ```

   

8. 写jsp页面

![image-20210924151518489](https://i.loli.net/2021/09/24/8io2T4PzwsmGySY.png)

