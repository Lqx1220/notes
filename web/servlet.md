### Web访问图

![image-20210810123612687](https://i.loli.net/2021/08/10/POM7bw3hajRtSVr.png)

### Servlet生命周期

![image-20210810123647802](https://i.loli.net/2021/08/10/ZelOmczJk39Nv4i.png)

Servlet 的整个生命周期过程的执行，均由 Web 服务器负责管理。

#### 生命周期方法执行流程

![image-20210810123811843](https://i.loli.net/2021/08/10/rl51sy8DYhxU3iK.png)

Servlet 生命周期方法的执行流程：

1 当请求发送到 Web 容器后， Web 容器会解析请求 URL ，并从中分离出 Servlet 对应的URI 。

2 根据分离出的 URI ，通过 web.xml 中配置的 URI 与 Servlet 的映射关系，找到要执行的Servlet ，即找到用于处理该请求的 Servlet 。

3 若该 Servlet 不存在，则调用该 Servlet 的无参构造器、 init() 方法，实例化该 Servlet 。然后执行 service() 方法。

4 若该 Servlet 已经被创建，则直接调用 service() 方法。

5 当 Web 容器被关闭，或该应用被关闭，则调用执行 destroy() 方法，销毁 Servlet 实例。

**用一个类实现Servlet但程序员可以获取到 Servlet 的这些生命周期时间点，并可以指定让 Servlet 做一些具体业务相关的事情。**

##### 注册Servlet

```xml
<servlet>
    <servlet-name>SomeServlet</servlet-name>
    <servlet-class>SomeServlet</servlet-class>
</servlet>
    <servlet-mapping>
        <servlet-name>SomeServlet</servlet-name>
        <url-pattern>/some</url-pattern>
    </servlet-mapping>
```

#### Servlet 特征

- Servlet 是单例多线程的 。
- 一个 Servlet 实例 只会执行一次无参构造器 与 init() 方法， 并且是在第一次访问时执行。
- 用户每提交一次对当前 Servlet 的请求，就会执行 一次 service() 方法。
- 一个 Servlet 实例只会执行一次 destroy() 方法，在应用停止时执行。
- 由于 Servlet 是单例多线程的，所以为了保证其线程安全性，一般情况下是不为 Servlet类定义可修改的成员变量的。因为每个线程均可修改这个成员变量,会出现线程安全问题。
- 默认情况下 Servlet 在 Web 容器启动时是不会被实例化的。

##### Web容器启动时创建Servlet实例

- 当值大于等于0 时表示容器在启动时就加载并初始化这个Servlet,数值越小,该Servlet的优先级就越高,其被创建的也就越早
- 当值小于0或者没有指定时则表示该 Servlet 在真正被使用 时才会去创建 。
- 当值相同时,容器会自己选择创建顺序

```xml
<servlet>
    <servlet-name>SomeServlet</servlet-name>
    <servlet-class>SomeServlet</servlet-class>
    <!--当值大于等于0 时表示容器在启动时就加载并初始化这个Servlet,数值越小,该Servlet的优先级就越高,其被创建的也就越早-->
    <load-on-startup>1</load-on-startup>
</servlet>
```

##### Web容器的两个Map

当 Servlet 实例被创建好后，会将该 Servlet 实例的引用存放到一个 Map 集合中。该 Map集合的 key 为 URI ，而 value 则为 Servlet 实例的引用，即 Map<String, Servlet> 。当 Web 容器从用户请求中分离出 URI 后，会首先到这个 Map 中查找是否存在其所对应的 value 。若存在，则直接调用其 service() 方法。若不存在，则需要创建该 Servlet 实例。

当 Web 容器从用户请求中分离出 URI 后，到第一个 Map 中又没有找到其所对应的 Servlet实例，则会马上查找这第二个 Map ，从中找到其所对应的类名，再根据反射机制，创建这个 Servlet 实例。 然后再将这个创建好的 Servlet 的引用放入到第一个 Map 中。

![image-20210810140823044](https://i.loli.net/2021/08/10/Ok3R76Jt1I49Umf.png)

### ServletConfig

#### 什么是ServletConfig

在web.xml文件中的`<servlet> </servlet>`中的配置信息就是servletConfig

#### 获取ServletConfig对象

1. 定义一个成员变量
2. 在init()方法中接收servletConfig

#### 示例:

```Java
public class SomeServlet implements Servlet {
	private ServletConfig servletConfig;
	 @Override
    public void init(ServletConfig servletConfig) throws ServletException {
       this.servletConfig  = servletConfig;
    }
    ...
}
```

初始化参数

```xml
<servlet>
<init-param>
        <param-name>name</param-name>
        <param-value>qinshihuang</param-value>
</init-param>
 <init-param>
        <param-name>age</param-name>
        <param-value>21</param-value>
</init-param>
    ...
</servlet>    
```

- getInitParameter():获取指定名称的初始化参数值。
- getInitParameterNames:获取当前 Servlet 所有的初始化参数名称。其返回值为枚举类`Enumeration<String>` 
- getServletName()：获取当前 Servlet 的 `<servlet name></servlet name>` 中指定的 Servlet 名称。
- getServletContext()：获取到当前 Servlet 的上下文对象 ServletContext 

```Java
@Override
public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
    final Enumeration<String> initParameterNames = servletConfig.getInitParameterNames();
    while (initParameterNames.hasMoreElements()) {
        final String s = initParameterNames.nextElement();
        final String initParameter = servletConfig.getInitParameter(s);
        System.out.println(s+"---->"+initParameter);
    }
}
```

![image-20210810154841468](https://i.loli.net/2021/08/10/5Y6RJQI7GgZESel.png)

### ServletContext

#### 什么是ServletContext

是Web应用中所有Servlet在Web容器中的运行时环境,一个Web应用只有一个ServletContext

#### 获取ServletContext

1. 获取ServletConfig
2. 获取ServletContext

#### 示例:

```Java
public class SomeServlet implements Servlet {
    public SomeServlet() {

    }
private ServletConfig servletConfig;
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
       this.servletConfig  = servletConfig;
    }

    @Override
    public ServletConfig getServletConfig() {

        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        final ServletContext servletContext = servletConfig.getServletContext();
        servletContext.setAttribute("baiqi",40);
        servletContext.setAttribute("mengtian",50);
        System.out.println("--------------------------------------");
        final Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            final String s = initParameterNames.nextElement();
            final String initParameter = servletContext.getInitParameter(s);
            System.out.println(s+"------->"+initParameter);
        }
    }


    @Override
    public String getServletInfo() {

        return null;
    }

    @Override
    public void destroy() {

    }
}
```

```Java
public class OtherServlet implements Servlet {
    private ServletConfig servletConfig;
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        this.servletConfig  = servletConfig;
    }


    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        final ServletContext servletContext = servletConfig.getServletContext();

        final Integer baiqi = (Integer) servletContext.getAttribute("baiqi");
        final Integer mengtian = (Integer) servletContext.getAttribute("mengtian");
        System.out.println(baiqi);
        System.out.println(mengtian);

    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

```xml
 <context-param>
        <param-name>country</param-name>
        <param-value>qin</param-value>
    </context-param>
    <context-param>
        <param-name>period</param-name>
        <param-value>warring states</param-value>
    </context-param>
    <servlet>
        <servlet-name>SomeServlet</servlet-name>
        <servlet-class>SomeServlet</servlet-class>
        <init-param>
            <param-name>name</param-name>
            <param-value>qinshihuang</param-value>
        </init-param>
        <init-param>
            <param-name>age</param-name>
            <param-value>21</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>SomeServlet</servlet-name>
        <url-pattern>/some</url-pattern>
    </servlet-mapping>
    <servlet>
        <servlet-name>otherSrevlet</servlet-name>
        <servlet-class>OtherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>otherSrevlet</servlet-name>
        <url-pattern>/other</url-pattern>
    </servlet-mapping>
```

![image-20210810173833757](https://i.loli.net/2021/08/10/RbmFgPk8JEzW65v.png)

### 欢迎页面

```xml
<welcome-file-list>
        <welcome-file>say01.jsp</welcome-file>
        <welcome-file>say02.jsp</welcome-file>
        <welcome-file>say03.jsp</welcome-file>
    </welcome-file-list>
```

### `<url-pattern/>`的设置与匹配

#### `<url-pattern/>`的设置

##### 精确路径模式

- 请求路径必须与`<url pattern/>` 的值完全相同 才可被当前 Servlet 处理。

  ```xml
  <url-pattern>/some/aaa</url-pattern>
  ```

##### 通配符路径模式

- 该模式中的路径由两部分组成:精确路径部分与通配符部分.请求路径中 只有 携带了<url pattern/> 值中指定的精确路径部分才可被当前 Servlet 处理。

  ```xml
  <url-pattern>/some/*</url-pattern>
  ```

##### 全路径模式

- 提交的所有请求全部可被当前的Servlet 处理。 其值可以指定为/*,也可指定为 /。

  ```xml
  <url-pattern>/*</url-pattern>
  ```

/*和/的区别:

- /*是真正的全路径模式,可以拦截所有请求

- /只会拦截静态资源请求,对于动态资源请求是不会进行拦截的

##### 后辍名模式

- 请求路径最后的资源名称必须携带`<url pattern/>` 中指定的后辍名，其请求才可被当前Servlet 处理。

  ```xml
  <url-pattern>*.do</url-pattern>
  ```

##### 注意事项

- 一个`<servlet mapping/>` 中可以配置多个 `<urlpattern/>`，即一个 Servlet 可以对多个请求路径进行处理。
- 为`<url pattern/>`设置值时,带斜杠的通配符路径模式与后辍名模式不能同时使用.

##### 匹配原则

1. 路径优先后辍匹配原则
2. 精确路径优先匹配原则
3. 最长路径优先匹配原则

### HttpServletRequset

#### 请求的生命周期

从浏览器将请求发送开始到服务器发送响应后结束

#### 请求参数

请求参数以Map的形式接收`Map<String,String[]>`

![image-20210810201438337](https://i.loli.net/2021/08/10/cHodkMCJx5sLAIp.png)

![image-20210810201448681](https://i.loli.net/2021/08/10/YaEvMwHeKAXlxhF.png)

#### 域属性

在Request 中也存在域属性空间，用于存放有名称的数据。该数据只在当前 Request 请求中可以进行访问。

#### 中文乱码问题

##### post提交:

通过`setCharacterEncoding()`设置编码

##### get提交

可以通过修改 Tomcat 默认字符编码的方式来解决 GET 提交方式中携带中文的乱码问题。

![image-20210810202526700](https://i.loli.net/2021/08/10/sxEPotJegD7bTOU.png)

##### 万能方案

1. 首先将请求参数以当前编码的形式先对单字节的数据进行编码
2. 将编码后的数据存放在字节数组中
3. 将字节数组中的数据按指定的UTF-8格式进行解码

```Java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    final String username = req.getParameter("username");
    final byte[] bytes = username.getBytes(StandardCharsets.ISO_8859_1);
    final String s = new String(bytes, StandardCharsets.UTF_8);
}
```

### HttpServletResponse

#### 向客户端发送数据

1. 创建输出流
2. 输出数据

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    final String username = req.getParameter("username");
    final byte[] bytes = username.getBytes(StandardCharsets.ISO_8859_1);
    final String s = new String(bytes, StandardCharsets.UTF_8);
    final String parameter = req.getParameter(s);
    final PrintWriter writer = resp.getWriter();
    writer.println("username is "+username);
    writer.println("age is "+parameter);
}
```

#### 响应乱码

`setContentType("text/html;charset=utf-8")`用于设置响应体的字符编码

`setCharacterEncoding("utf-8")`用于修改`setContentType("text/html;charset=utf-8")`中设置的编码

**这些设置必须在输出流创建之前设置**

### 请求转发和重定向

![image-20210810210155104](https://i.loli.net/2021/08/10/iblfG3orYnEhv8m.png)

#### 请求转发

- 请求转发是指，资源1 在服务器内部，直接跳转到的资源 2 ，所以请求转发也称为服务器内跳转。整个过程浏览器只发出一次请求，服务器只发出一次响应。所以，无论是资源 1还是资源 2 ，整个过程中，只存在一次请求，即用户所提交的请求。所以，无论是资源 1 还是资源 2 ，它们均可从这个请求中获取到用户提交请求时所携带的相关数据。

- ```java
  req.getRequestDispatcher("otherServlet").forward(req,resp);
  ```

#### 重定向

- 重定向是指，资源1 需要访问资源 2 ，但并未在服务器内直接访问，而是由服务器自动向浏览器发送一个响应 ，浏览器再自动提交一个新的请求，这个请求就是对资源 2 的请求。对于资源 2 的访问，是先跳出了服务器，跳转到了客户端浏览器，再跳回到了服务器。所以重定向又称为服务器外跳转。这样的话，就出现了一个问题：资源2中是无法获取到用户提交请求中的数据的。它只能获取到第二次请求中所携带的数据。
  
- ```Java
  resp.sendRedirect("otherServlet");
  ```

#### 重定向时的数据传递

携带请求参数的形式为

请求路径？参数名=值&参数名=值

```java
		final String someName = req.getParameter("username");
        final String someAge = req.getParameter("age");
        req.getRequestDispatcher("otherServlet").forward(req,resp);
        resp.sendRedirect("otherServlet?someName="+someName+"&someAge"+someAge);
```

#### 重定向乱码问题

1. someServlet先按指定编码格式对字符串进行编码
2. otherServlet再按指定编码格式对字符串进行解码
3. 再在otherServlet中将字节数组中的数据按指定的UTF-8格式进行解码

![image-20210810213849308](https://i.loli.net/2021/08/10/xp6HXm9otdfZqTc.png)

![image-20210810213908742](https://i.loli.net/2021/08/10/zf3CN46k8uPxBes.png)

#### 请求转发和重定向的对比

请求转发

- 浏览器只发出一次请求，收到一次响应
- 请求所转发到的资源中可以直接获取到请求中所携带的数据
- 浏览器地址栏显示的为 用户所提交的请求路径
- 只能跳转到当前应用的资源中

重定向

- 浏览器发出两次请求，接收到两次响应
- 重定向得到的资源不能直接获取到用户提交请求中所携带的数据
- 浏览器地址栏显示的为重定向的请求路径，而非用户提交请求的路径。也正因为如此，
  重定向的一个很重要作用是:防止表单重复提交
- 重定向不仅可以跳转到当前应用的其它资源，也可以跳转到到其它应用中资源

### RequestDispatcher

#### forward()与include()区别

主要表现在标准输出流的开启时间不同

使用forward则当前请求还未开始,需要继续向前所以服务器就不会打开标准输出流

使用include则说明当前请求已经结束,可对客户端响应,不仅要将自己的数据写到输出流还要将其他数据包含到自己的输出流中

![image-20210811184802310](https://i.loli.net/2021/08/11/lYZhvMpqX3txeN5.png)

![image-20210811184832049](https://i.loli.net/2021/08/11/ZVjb9Qvx2ReC1yW.png)

### 路径访问问题

绝对路径 = 参照路径 + 相对路径

#### 以"/"开头的相对路径

##### 前台路径

所谓前台路径是指，由浏览器解析执行的代码中所包含的路径.

前台路径的参照路径是Web服务器的根路径`http://127.0.0.1:8080/`

##### 后台路径

所谓后台路径是指，由服务器解析执行的代码及文件中所包含的路径。

后台路径的参照路径是Web应用的根路径 。如 http://127.0.0.1:8080/primary 。

##### 后台路径特例

当代码中使用 response 的 sendRedirect() 方法进行重定向时,若其参照路径是以斜杠开头,则其参照路径不是 web 应用的根路径,而是 web服务器的根路径。 

只有response.sendRedirect这一种重定向是特例

为什么这里是特例？因为sendRedirect() 方法可以重定向到其它应用，若不指定要跳转的应用，其将无法确定跳转方向。

#### 以路径名称开头的相对路径

无论是出现在前台页面 ，还是出现在后台 Java 代码或配置文件中 其参照路径 都 是当前访问路径的资源路径