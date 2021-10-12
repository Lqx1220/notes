## 局部刷新

#### 原理

1. 不能由浏览器发送请求给服务端
2. 浏览器委托浏览器内存中的一个脚本对象代替浏览器发送请求
3. 这个行为导致服务端直接将响应包发送脚本对象(异步请求对象)内存中
4. 这个行为导致脚本对象的内容被覆盖掉,但此时浏览器内存中绝大部分内容没有受到任何影响
5. 这个行为导致浏览器在展示数据时候,同时展示原有数据和响应数据



AJAX是实现局部刷新的一种技术

## 异步请求对象

#### 作用

- 在不重新加载页面的情况下更新网页
- 在页面已加载后 向 服务器请求数据
- 在页面已加载后从服务器接收数据

#### AJAX异步实现

###### onreadstatechange事件

- 作用:当异步对象发起请求，获取了数据都会触发这个事件。
- 用法:这个事件需要指定一个函数， 在函数中处理状态的变化。
-  异步对象的属性 readyState 表示异步对象请求的状态变化
  -  0：创建异步对象时， new XMLHttpRequest();
  -  1: 初始异步请求对象， xmlHttp.open()
  -  2：发送请求， xmlHttp.send()
  -  3: 异步对象接收应答数据 从服务端返回数据。XMLHttpRequest 内部处理。
  -  4：异步请求对象已经将数据解析完毕。  此时才可以读取数据。
- status 属性：表示网络请求的状况的

```js
 xmlHttp.onreadystatechange= function(){
      // 处理请求的状态变化。
		 if(xmlHttp.readyState == 4 && xmlHttp.status== 200 ){
           //可以处理服务器端的数据，更新当前页面
			  var data = xmlHttp.responseText;
			  document.getElementById("name").value= data;
		 }
    }
```

###### 创建对象方式

```js
var xmlhttp=new XMLHttpRequest();
```

###### 初始异步请求对象

```js
  xmlHttp.open(请求方式get|post, "服务器端的访问地址", 同步|异步请求（默认是true，异步请求）)
//  xmlHttp.open("get", "loginServlet?name=zs&pwd=123",true);
```

###### 使用异步对象发送请求

```js
 xmlHttp.send()
```

###### 获取服务器端返回的数据

- 使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。 
  - responseText：获得字符串形式的响应数据 
  - responseXML：获得 XML 形式的响应数据

```js
xmlHttp.responseText 
```

## json

#### 作用:

JSON是JavaScript对象标记,是一种标准的数据交换格式

#### 特点:

体积小,易解析

#### 格式

###### JSON 对象

```json
{key1 : value1, key2 : value2, ... keyN : valueN }
//{ "name":"菜鸟教程" , "url":"www.runoob.com" }
```

###### JSON 数组

```json
[
    { key1 : value1-1 , key2:value1-2 }, 
    { key1 : value2-1 , key2:value2-2 }, 
    { key1 : value3-1 , key2:value3-2 }, 
    ...
    { keyN : valueN-1 , keyN:valueN-2 }, 
]


{
    "sites": [
        { "name":"菜鸟教程" , "url":"www.runoob.com" }, 
        { "name":"google" , "url":"www.google.com" }, 
        { "name":"微博" , "url":"www.weibo.com" }
    ]
}
```

#### java对象转化为json

```java
ObjectMapper om = new ObjectMapper();
String json = om.writeValueAsString(xxx)//xxx为Java对象
```

#### JSON 文本转换为 JavaScript 对象

```js
var obj = eval ("(" + txt + ")");//txt为json文本
```

