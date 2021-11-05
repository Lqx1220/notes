### cookie和session区别

- Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。
- Session 则是将会话状态保存在了服务器端。

### session原理

- 写入Session列表

服务器对当前应用中的 Session 是以 Map 的形式进行管理的，这个 Map 称为 Session 列表。该 Map 的 key 为一个 32 位长度的随机串，这个随机串称为 JSessionID，value 则为 Session对象的引用。 当用户第一次提交请求时，服务端 Servlet 中执行到 request.getSession()方法后，会自动生成一个 Map.Entry 对象，key 为一个根据某种算法新生成的 JSessionID，value 则为新创建的 HttpSession 对象。 

- 服务器生成并发送 Cookie

在将 Session 信息写入 Session 列表后，系统还会自动将“JSESSIONID”作为 name，这个 32 位长度的随机串作为 value，以 Cookie 的形式存放到响应报头中，并随着响应，将该Cookie 发送到客户端。

![image-20210811205019101](https://i.loli.net/2021/08/11/lBYSwjQKaJOndsu.png)

- 客户端接收并发送 Cookie

客户端接收到这个 Cookie 后会将其存放到浏览器的缓存中。即，只要客户端浏览器不关闭，浏览器缓存中的 Cookie 就不会消失。 

当用户提交第二次请求时，会将缓存中的这个 Cookie，伴随着请求的头部信息，一起发送到服务端

![image-20210811205036751](https://i.loli.net/2021/08/11/aqBekUruyhWMtn8.png)

- 从 Session 列表中查找

服务端从请求中读取到客户端发送来的 Cookie，并根据 Cookie 的 JSSESSIONID 的值，从Map 中查找相应 key 所对应的 value，即 Session 对象。然后，对该 Session 对象的域属性进行读写操作

### Session 的失效

在 web.xml 中可以通过`<session-config/>`标签设置 Session 的超时时间，单位为分钟。默认 Session 的超时时间为 30 分钟。

这个时间并不是从 Session 被创建开始计时的生命周期时长，而是从最后一次被访问开始计时，在指定的时长内一直未被访问的时长。 

### Cookie 禁用后使用 Session 进行会话跟踪 

#### 手工重写 URL

将 JSessionID 添加到地址栏的请求最后。key 为全小写的 jsessionid，而 value 则为服务端响应头部发送来的 JSESSIONID。不过，需要的是原请求与 jsessionid 间使用分号分隔，而非问号。

**从 Session 对象的创建开始，一直到最终的 Session 失效，这才是一次会话。**

####  重定向的 URL 重写

HttpServletResponse 具有一个方法 encodeRedirectURL()，可以完成对重定向 URL 的重写，即在重定向的路径后会自动添加 jsessionid。

<img src="https://i.loli.net/2021/08/11/UIu6cOHQ5YzK3Li.png" alt="image-20210811211439247" style="zoom: 67%;" />

<img src="https://i.loli.net/2021/08/11/QR6Imd8wKvGVJ3p.png" alt="image-20210811211449432" style="zoom: 67%;" />

#### 超链接的 URL 重写

HttpServletResponse 具有一个方法 encodeURL()，可以完成对类似对超链接这样的非重定向页面跳转的 URL 的重写，即在其路径后会自动添加 jsessionid。 

<img src="https://i.loli.net/2021/08/11/aNpY5EFtTRxsk3L.png" alt="image-20210811211607461" style="zoom:67%;" />

### 域属性空间

- ServletContext，即 application，置入其中的域属性是整个应用范围的，可以完成跨会话共享数据。
-  HttpSession，置入其中的域属性是会话范围的，可以完成跨请求共享数据。
-  HttpServletRequest，置入其中的域属性是请求范围的，可以完成跨 Servlet 共享数据。但这些 Servlet 必须在同一请求中。 

