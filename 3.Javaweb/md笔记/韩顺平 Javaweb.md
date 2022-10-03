# 一、Javaweb技术

## HTML/CSS

## JavaScript

**三件套的关系及网页的组成**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281507609.png" alt="image-20220928150700475" style="zoom:80%;" />



 js代码写在script标签中，scrpt 标签的位置，可以在 head 中，也可以在 body 体。

```html
<!DOCTYPE html> <html lang="en"> 
    <head> 
        <meta charset="UTF-8"> 
        <title>标题</title> 
        <script type="text/javascript"> 
            window.alert("hello, Javascript"); 
        </script> 
    </head> 
    <body> 
        <script type="text/javascript"> 
            window.alert("hello, 韩顺平教育"); 
        </script> 
    </body> 
</html>
```

-  type="text/javascript" 表示这个脚本(script)类型是javascript，可以不写，但是建议写上。



## **DOM文档对象模型**

就是把文档中的标签，属性，文本，转换成为对象来管理。

![image-20220927205638055](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209272056133.png)

## **XML**

## **Tomcat**

是web容器中的一种，也是Java Servlet，jsp等技术的开源实现。

作用是：暴露端口，按照特定资源URL找到处理的servlet。然后处理请求。

## **Servlet（三大组件）**

### 1.原理简介

servlet就是一个Java接口，是JavaEE规范的一种，主要是为了扩展Java作为Web服务的功能。由其他内部厂商如tomcat，JBoss内部实现web的功能。不在jdk里，需要导入新的包。

**Servlet** **在** **JavaWeb** **项目位置**

![image-20220928155228402](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281552510.png)


```java
public class HelloServlet implements Servlet {
    /**
     * 1.初始化 servlet
     * 2.当创建HelloServlet 实例时，会调用init方法
     * 3. 该方法只会被调用一次
     */
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init() 被调用");
    }

    /**
     * 返回ServletConfig 也就是返回Servlet的配置
     */
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /**
     * 1. service方法处理浏览器的请求(包括get/post)
     * 2. 当浏览器每次请求Servlet时，Servlet实例就会调用一次service()方法
     * 3. 当tomcat调用service()方法时，还会把http请求的数据封装成实现ServletRequest接口的request对象
     * 4. 通过servletRequest 对象，可以得到用户提交的数据
     * 5. servletResponse 对象可以用于返回数据给tomcat->浏览器
     */
    @Override
    public void service(ServletRequest servletRequest,
                        ServletResponse servletResponse) throws ServletException, IOException {
        //HelloServlet是单例的
        //Tomcat每处理一次http请求，就生成一个新的线程
        
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        String method = httpServletRequest.getMethod();
        if("GET".equals(method)) {
            doGet(); //用doGet() 处理GET请求
        } else if("POST".equals(method)) {
            doPost(); //用doPost() 处理POST请求
        }
    }

    /**
     * 用于响应get请求的
     */
    public void doGet() {
        System.out.println("doGet() 被调用..");
    }

    /**
     * 用于响应post请求的
     */
    public void doPost() {
        System.out.println("doPost() 被调用..");
    }

    /**
     * 返回servlet信息，使用较少
     * @return
     */
    @Override
    public String getServletInfo() {
        return null;
    }

    /**
     * 1. 该方法是在servlet销毁时（Web服务停止/Servlet容器被销毁）调用。
     * 2. 只会调用一次
     */
    @Override
    public void destroy() {
        System.out.println("destroy() 被调用...");
    }
}
```

### 2.使用

**实际开发中，都是继承HttpServlet抽象类更方便：**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281603630.png" alt="image-20220928160336562" style="zoom:80%;" />

​	继承HttpServlet抽象类，只需重写doGet( )或doPost( )方法。HttpServlet抽象类中service方法已经写好。



注：如果在<servlet>元素中配置了一个<load-on-startup>元素属性为1，那么**WEB** **应用程序在启动时，就会装载并创建** **需要的Servlet** **的实例对象、以及调用** **Servlet** **实例对象的** **init()方法.**（这样启动时候久一些，但启动后访问网页时候响应更快。）

### 3.其他相关类

#### **①ServletConfig类**

每一个servlet类都有一个它的ServletConfig类，包含自己配置信息。

![image-20220928162827002](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281628056.png)

作用：

- 获取Servlet实例的servlet-name值（web.xml配置servlet的信息）
- 初始化参数init-param（web.xml配置servlet的信息）

​	<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281634552.png" alt="image-20220928163443496"  />

- servletContext对象。

#### **②ServletContext类** 

可以被多个servlet实例共享的一个对象（实现多个servlet信息存储/通信等），ServletContext类用来是管理这些servlet类的。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281638697.png" alt="image-20220928163801644"  />

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281638653.png" alt="image-20220928163812597"  />

![image-20220928164101383](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281641470.png)

作用：

- 像 Map 一样存取数据, 多个 Servlet 共享数据

- 获取 web.xml 中配置的上下文参数 context-param [信息和整个 web 应用相关，而不是 属于某个 Servlet]

​	![image-20220928164553329](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281645381.png)

- 获取当前的工程路径，格式: /工程路径 =》 比如 /servlet
- 获 取 工 程 部 署 后 在 服 务 器 硬 盘 上 的 绝 对 路 径 ( 比 如 : D:\hspedu_javaweb\servlet\out\artifacts\servlet_war_exploded)

#### ③**HttpServletRequest**

​	每次HTTP请求，Tomcat会创建一个HttpServletRequest对象 (并把请求的信息放入) 传递给Servlet程序。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281711135.png" alt="image-20220928171111043" style="zoom:80%;" />

相较于与ServletRequest接口，HttpServletRequest接口多了一些针对于Http协议的方法。 例如：getHeader()， getMethod() ， getSession() 。

HttpServletRequest常用方法：

1. getRequestURI() 获取请求的资源路径 /servlet/loginServlet 
2. getRequestURL() 获 取 请 求 的 统 一 资 源 定 位 符 （ 绝 对 路 径 ） http://localhost:8080/servlet/loginServlet 
3. getRemoteHost() 获取客户端的主机, getRemoteAddr() 
4.  getHeader() 获取请求头
5. **getParameter() 获取请求的参数**
6. getParameterValues() 获取请求的参数（多个值的时候使用） , 比如 checkbox, 返回的数组
7.  getMethod() 获取请求的方式 GET 或 POST
8. **setAttribute(key, value); 设置域数据** 
9. **getAttribute(key); 获取域数据**
10. **getRequestDispatcher() 获取请求转发对象, 请求转发的核心对象。（多个sevlet按序执行时）**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281729181.png" alt="image-20220928172954127" style="zoom:80%;" />

##### **请求转发**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281737754.png" alt="image-20220928173748690" style="zoom:80%;" />

1. HttpServletRequest对象(也叫Request对象)提供了一个 getRequestDispatcher()方法 (下图) ，该方法返回一个RequestDispatcher对象，调用这个对象的forward( )方法可以实现请求转发。

- getRequestDispatcher( )方法：需要传入string类型的访问地址，如“/manageServlet"

![image-20220928174102814](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281741852.png)

- RequestDispatcher对象的forward()方法：转发给下一个servlet，需要传入request和response对象。

![image-20220928174227804](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281742843.png)

2. HttpServletRequest对象同时也是一个域对象，开发人员通过 request 对象在实现转发时，可以把数据通过 request 对象带给其它 web 资源处理。下面是request对象存储数据相关的方法：

- setAttribute方法 
  getAttribute方法 
  removeAttribute方法 
  getAttributeNames方法









注意：若通过getParameter() 获取到的参数乱码的解决办法，获取参数前先设置编码格式。

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281728836.png" alt="image-20220928172812742" style="zoom:80%;" />

​		若response对象通过PrintWriter writer返回数据给浏览器不显示/乱码，需要设置编码.

​		![image-20220928175135524](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281751583.png)



#### ④**HttpServletResponse**

每次 HTTP 请求，Tomcat 会创建一个 HttpServletResponse 对象传递给 Servlet 程序去使用。

![image-20220928175743080](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281757163.png)

**Response对象返回数据方法：**

![image-20220928175951438](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281759517.png)

##### 请求重定向

不能共享 Request 域中的数据，本质是两次 http 请求，会生成两个 HttpServletRequest 对象。

![image-20220928180227947](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281802015.png)

response对象调用 sendRedirect()方法，参数为新地址。

![image-20220928180427403](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281804444.png)

```java
//动态获取到 application context
	String contextPath = getServletContext().getContextPath(); 
	System.out.println("contextPath= " + contextPath); 
	response.sendRedirect(contextPath + "/downServletNew");
```







## HTTP协议

### 1.请求包

- 请求行
- 请求头
- 请求体（Post）

**请求包分析GET：**

![image-20220928185534716](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281855887.png)

**请求包分析POST：**

![image-20220928185455348](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281854575.png)

Post请求方法，请求数据放到了请求体中。

### 2.响应包

- 响应行
- 响应头
- 响应体

![image-20220928190630874](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281906968.png)

### 3.常见的状态码

![image-20220928191048645](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281910716.png)

### 4.MIME类型

MIME 是 HTTP 协议中数据类型。 MIME 类型的格式是"大类型/小类型"， 并与某一种文件的扩展名相对应。

在HTTP 协议请求体/响应体有体现，如图：

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281916062.png" alt="image-20220928191603926" style="zoom:50%;" />

常见的MIME类型（最右边 ）：

![image-20220928191623205](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281916278.png)





## Cookie&Session

**会话：**用户开一个浏览器，点击多个超链接，访问服务器多个 web 资源，然 后关闭浏览器，整个过程称之为一个会话。

**会话的两种技术：**Cookie、Session

### Cookie

**客户端技术**（cookie数据保存在浏览器），服务器把每个用户的数据以 cookie 的形式写给用户各自的浏览器。当用户使用浏览器再去访问服务器中的 web 资源时，就会带着本地cookie的数据去访问。

**1.使用**

1. Cookie数据存储：key-value，并且都是String类型。

2. Cookie(在服务端创建的)

```java
Cookie c = new Cookie(String name,String val);
c.setMaxAge();//保存时间
```

3. 添加一个Cookie给客户端

```java
response.addCookie(c);
```

4. 读取/修改 cookie(在服务器端读取到 cookie 信息)

```java
Cookie[] cookies = request.getCookies();
```

**2.底层原理**

<img src="C:/Users/10275/AppData/Roaming/Typora/typora-user-images/image-20220930172654059.png" alt="image-20220930172654059" style="zoom:80%;" />

- 服务器创建好Cookie对象后，发回响应（响应头里）返回个浏览器。
- 浏览器每次访问网页（一个web工程为单位），会自动带上该网站关联的cookie（如果有），即封装到request对象里面。

- **JsessionID是作为一次会话中浏览器唯一标识（**浏览器每次访问一个网站，都会给该网站发送一个cookie对象 JSESSIONID = xxxx，让网站知道是哪个客户端发送过来的）

**3.生命周期**

默认为一次会话后删除。

自定义 setMaxAge( ):

- 正数，在指定秒后过期。

- 负数，会话结束后删除（默认值-1）	

- 0，马上删除。

**4.Cookie可设置有效路径**

哪些网站会发生，哪些不发。

**5.Cookie的限制**

cookie 的总数量没有限制，但是每个域名的 COOKIE 数量和每个 COOKIE 的大小是有限制的 (不同的浏览器限制不同, 知道即可) , Cookie 不适合存放数据量大的信息。

**6.作用**

cookie是保存客户端浏览记录，记住密码等。



### Session

**服务器端技术**，服务器在运行时为每一个用户的浏览器创建一个其独享的 session 对象/集合。

**1.使用**

存储结构：key-value。key为String类型，value为object类型。

```java
//1.创建和获取Session. 第 1 次调用是创建 Session 会话， 之后调用是获取创建好的 Session 对象
	HttpSession session = request.getSession();
//2. 向 session 添加属性 
    session.setAttribute(String name,Object val); 
//3. 从 session 得到某个属性 
    Object obj = session.getAttribute(String name);
//4. 从 session 删除调某个属性: 
	session.removeAttribute(String name); 
//5. 判断是不是刚创建出来的 Session
	session.isNew(); 
//6. 每个 Session 都有1个唯一标识 Id 值。通过getId()得到Session的会话id值
	session.getId();
```

**2.原理**

![image-20220930174843894](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301748006.png)

**3.生命周期**

​	指的是 客户端/浏览器两次请求最大间隔时长，而不是累积时长。 

**默认：**

​	Session 默认的超时为 30 分钟， 可以在 tomcat 的 web.xml 设置。

**自定义：**

public void setMaxInactiveInterval(int interval) 设置 Session 的超时时间（以秒为单位）， 超过指定的时长，Session 就会被销毁。 

- 值为正数的时候，设定 Session 的超时时长。 

- 负数表示永不超时 

public int getMaxInactiveInterval()获取 Session 的超时时间 

public void invalidate() 让当前 Session 会话立即无效 

**4.作用**

session一次会话中，存储数据用的。



## JSP [过时技术]

**出现由来：**

​	![image-20220930175832211](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301758322.png)

**怎么理解jsp底层是一个servlet：**

​	从原先浏览器访问servlet(serclet转发页面，或者动态修改页面)，到现在直接访问jsp页面。

![](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301805146.png)

- jsp里面可以写java代码，也可以通过request动态获取数据。jsp创建好后，tomcat就为其提供了9内置对象与4域对象（如request，response，session，ServletContext等），直接使用即可。

- 总结：jsp = 页面+servlet

**其他 jsp 衍射技术**

1.EL 表达式

​	EL表达式为了代替 jsp 页面的表达式脚本<%=request.getAttribute("xx")%> ，EL 表达式输出数据的时比 jsp 的表达式脚本简洁。

​	EL 表达式基本语法： ${key1}。

2.JSTL 标签库 

​	JSTL 是为了替换代码脚本。这样 jsp 页面 变得更佳简洁。

​	JSTL 由五个标签库组成 ，使用 JSTL，需要导入相关的 jar 包。

**作用**

服务器渲染技术。类似有Thymeleaf（但这个会和springboot结合）。



## Listener（三大组件）

监听器的作用是，监听某种变化 (一般就是对象创建/销毁, 属性变化), 触发对应方法完成相应的任务。需要实现相应接口。

- 接口**ServletContextListener**（监听ServletContext实例，其中ServletContext对象会在Web应用启动时被自动创建），当监听的实例被创建/销毁/时，该实现类给出相应的操作。

​		**ServletContextAttributeListener**（监听ServletContext的属性变化）

- 其他监听器接口（使用较少）：					

​		**HttpSessionListener**（监听Session实例创建/销毁），**HttpSessionAttributeListener**（监听Session实例的属性变化）

​		**HttpSessionListener**（监听Request实例创建/销毁），**HttpSessionAttributeListener**（监听Request实例的属性变化）

​		等等...

```java
public class MyServletContextListener implements ServletContextListener { 
    
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) { 
        System.out.println("ServletContext 创建，完成 WEB 项目初始化的工作.."); 
    }
    
    @Override public void contextDestroyed(ServletContextEvent servletContextEvent) { 
        System.out.println("ServletContext 销毁, 完成资源回收工作.."); 
    }
}
```





## Filter（三大组件）

请求和响应都会经过Filter。若要创建自定义 Filter，需要实现 [javax.servlet.Filter](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/Filter.html) 接口。

![image-20220928151843888](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281518946.png)

```java
public class MyFilter implements Filter {
 	//1.web工程启动时，就会执行构造器和init方法。
    @Override
    public void init(FilterConfig filterConfig){ 
        System.out.println("ManageFilter init()被调用."); 
    }
    
    //2.过滤的url-pattern匹配到了访问地址，便会执行
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain){ 
        //具体过滤的操作
        System.out.println("ManageFilter doFilter()被调用.");
        //若通过了则执行
        filterChain.doFilter(servletRequest, servletResponse);
    }
    
    //3.web工程停止时，就会执行destroy方法销毁Filter实例。
    @Override public void destroy() { 
        System.out.println("ManageFilter destroy()被调用。"); 
    }
    
}
```

**FilterChain** 过滤链

![image-20220928153925843](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209281539891.png)



## JQuery

对原生JavaScript代码的封装，提供了很多事件、API、选择器供使用，使用户能更方便地处理 HTML,css,dom。流行技术。

**使用**

导入Jquery库文件，再使用标签 script 引入 jQuery 库文件即可使用。(一次引入，整个文件都可使用)

```html
<script type="text/javascript" src="./script/jquery-3.6.0.min.js"></script>
```

**Jquery对象**

原生开发 js+DOM，现在直接使用JQuery。

​	在变量前面加上$，那么获取到的就是 jQuery 对象。jQuery 对象就是对 DOM 对象进行包装后产生的对象。

​	如果一个对象是 jQuery 对象, 那么它就可以使用 jQuery 里的方法，如$("#id").html();

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301954299.png" alt="image-20220930191751005"  />

![image-20220930192427035](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301954302.png)

**jQuery** **选择器**

核心部分。对事件处理, 遍历 DOM 和 Ajax 操作都依赖于选择器。

![image-20220930192310354](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301954945.png)

通过元素id, 标签名 和class来获取 DOM 元素。

1、Id

```javascript
//Id用法：#id值 
$("#myDiv"); 
//返回值 单个元素的组成的集合
//说明: 这个就是直接选择 html 中的 id=”myDiv” 
```

2、标签名

```js
//标签用法: 直接输入标签
$("div") 
//返回值 集合元素 
//说明: 就是 html 已经定义的标签 元素,例如 div, input, a 等等. 
```

3、class

```js
//class用法: .class名
$(".myClass") 
//返回值 集合元素 
//说明: 这个标签是直接选择 html 代码中 class=”myClass”的元素或元素组(因为在同一 html 页面中 class 是可以存在多个同样值的).
```

其他用法：

```js
//多个选择器一起使用，返回多个对象
$("div,span,p.myClass") 
//返回值 集合元素 
//说明: 将每一个选择器匹配到的元素合并后一起返回.你可以指定任意多个选择器, 并 将匹配到的元素合并到一个结果内.其中 p.myClass 是表示匹配元素 p class=”myClass”
```



## 数据交换JSON

**前端接收json数据：**

​	JSON 文本格式在语法上与创建 JavaScript 对象的代码相同。由于这种相似性，无需解析器，JavaScript 程序能够使用内建的 eval() 函数，用 JSON 数据来生成原生的 JavaScript 对象。

**后端接收json数据：**需要使用API转换为java数据类型。

1. java 中使用 json，需要引入到第 3 方的包 gson.jar 

2. Gson 是 Google 提供的用来在 Java 对象和 JSON 数据之间进行映射的 Java 类库。
3. 可以对 JSON 字符串 和 Java 对象相互转换

## 异步请求Ajax

### 介绍

AJAX 即"Asynchronous Javascript And XML"(异步 JavaScript 和 XML) 。

Ajax 是一种浏览器**①异步发起请求(指定发哪些数据)**，**② 局部更新页面的技术**。



**传统的方法**

![image-20220930195325368](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209301954562.png)

**AJax 技术**

![image-20220930200054857](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302000979.png)

![image-20220930200137502](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302001608.png)



### 使用

javaScript原生的Ajax的请求。

Ajax无非是对XMLHttpRequest 对象（XHR）的操作，即创建xhr，xhr发送请求，xhr响应请求。

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<script>
function loadXMLDoc()
{
	var xmlhttp = new XMLHttpRequest();
    //绑定事件，当servlet成功响应后执行的操作。function里面编写操作。
    //下面例子是：发送请求后修改id=“myDiv”的div标签的内容。
	xmlhttp.onreadystatechange=function()
	{
		if (xmlhttp.readyState==4 && xmlhttp.status==200)
		{
            //xhr的responseText属性值是服务器响应返回的值。
			document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
		}
	}
    //设置请求（请求方式、请求地址、是否异步）
	xmlhttp.open("GET","/try/ajax/ajax_info.txt",true);
    //发送请求
	xmlhttp.send();
}
</script>
</head>
<body>

<div id="myDiv"><h2>使用 AJAX 修改该文本内容</h2></div>
<button type="button" onclick="loadXMLDoc()">修改内容</button>

</body>
</html>
```

**使用JQuery的Ajax请求，更方便。**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标题</title>
    <!--引入jquery-->
    <script type="text/javascript" src="./script/jquery-3.6.0.min.js"></script>
    <script type="text/javascript">
        $(function () {
            //绑定事件
            $("#btn1").click(function () {
                //发出ajax
                /**
                 * 老师解读
                 * 1. 指定参数时，需要在{}
                 * 2. 给参数时，前面需要指定参数名
                 * 3. dataType: "json" 要求服务器返回的数据格式是json
                 */
                    $.ajax({
                        url: "/ajax/checkUserServlet2",
                        type: "POST",
                        data: { //这里我们直接给json, 为啥我要传日期, 为了浏览器缓存
                            username: $("#uname").val(),
                            date: new Date()
                        },
                        error: function () { //失败后的回调函数
                            console.log("失败~")
                        },
                        success: function (data, status, xhr) {
                            console.log("成功");                         
                        },
                        dataType: "json"
            })        
    </script>
</head>
<body>
</body>
</html>
```

![image-20220930210447742](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302104827.png)



## 线程安全ThreadLocal

1. ThreadLocal 的作用，可以实现在同一个线程数据共享, 从而解决多线程数据安全问题. 
2. ThreadLocal 可以给当前线程关联一个数据(普通变量、对象、数组)set 方法。
3. ThreadLocal 可以像 Map 一样存取数据，key 为当前线程, get 方法 
4. 每一个 ThreadLocal 对象，只能为当前线程关联一个数据，如果要为当前线程关联多个数 据，就需要使用多个 ThreadLocal 对象实例 
5. 每个 ThreadLocal 对象实例定义的时候，一般为 static 类型 
6. ThreadLocal 中保存数据，在线程销毁后，会自动释放





## Web文件上传下载

### 上传

要求为post请求(即form表单)，声明enctype类型为="multipart/form-data"。

![image-20220930211204086](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302112249.png)

![image-20220930211302575](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302113729.png)

### 下载

![image-20220930211554786](https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209302115953.png)



# 二、前端技术栈

三件套：html/css/**JavaScript**

<img src="https://raw.githubusercontent.com/LifeSum12/typora-image/main/img/202209272118339.png" alt="image-20220927211843252" style="zoom:80%;" />

- **ES6**（javaScript新版本）
- JQuery（javaScript工具库）
- **Vue**（类似JavaScript的框架）
- React（微服务架构的技术）

Axios+vue实现Ajax技术（改进异步请求Ajax）
